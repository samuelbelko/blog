@def title = "GSoC'23: Bayesian Optimization in Julia"

# GSoC'23: Bayesian Optimization in Julia

By **Samuel Belko**, published on September 25, 2023.

*The following blog post is my final report for Google Summer of Code 2023.*

\tableofcontents <!-- you can use \toc as well -->


## Introduction

In the following post, I describe my open source contributions to *The Julia Language* organization, supervised by Johanni Brea. The name of the project is «Implementing Scalable Bayesian Optimization in Julia». The initial project description can be found [here](https://summerofcode.withgoogle.com/programs/2023/projects/oJe84fwV).


## Preliminaries

Let me first briefly introduce the main concepts I will be often referring to.

### Gaussian Processes

Gaussian processes (GPs) constitute a family of stochastic processes that are widely used in Bayesian optimization and related fields. They represent a generalization of multi-variate Gaussian distribution into infinite dimensions. Usually we introduce hyperparameters that define a subset of GPs that are in some way related and reasonable parametrized.

One important reason why they are so useful is that once we fix finitely many indices from the infinite index set and consider the joint distribution of random variables corresponding to those fixed indices, we get a multi-variate Gaussian random variable (MVGRV). Hence, in practice we can work with simple MVGRVs that are however *automatically consistent* with the infinite dimensional model, existing purely in the conceptual world.

### Bayesian Optimization algorithms

Bayesian optimization (BO) algorithms are sample efficient techniques for optimizing functions which exhibit one or more of the following properties:

- are expensive to evaluate, e.g, an evaluation might correspond to running a lab experiment
- are restricted by a limited evaluation budget, e.g., as in crop breeding - one experiment per year
- evaluations can be corrupted by noise
- are non-convex
- are black box function, i.e., they lack a closed-form expression

This class of functions contains many practical problems, e.g., from chemical engineering and materials discovery. A prominent use-case is automated configuration of algorithms, such as MILP solvers or tuning of hyperparameters of machine learning models.

BO is a family of algorithms based on iteratively adapting a random field, such as a GP. By a random field we mean a collection of random variables indexed by the domain of the objective function. The random field is a surrogate model that encodes our beliefs about the objective function throughout optimization. 
Such a surrogate processes evaluations and this processed information is commonly used for defining an acquisition function, which quantifies the potential utility of evaluating the objective at a point in the domain. The next point where the algorithm evaluates the objective function is usually a maximizer of an acquisition function defined by the current information from the surrogate.

Traditional methods work well for lower dimensinal problems with continuous domains, as mentioned in (\cite{tut}).

If you are interested to learn more, please have a look at (\cite{tut}) and (\cite{garnett}).


### Trust Region Bayesian Optimization algorithm

Trust Region Bayesian Optimization (TuRBO) algorithm belongs to recent advances in BO that aim to work well in higher dimensions. The TuRBO algorithm was introduced in (\cite{Turbo}).

The idea is to maintain local Gaussian process surrogates that are accurate inside respective trust regions that adapt in size and postion based on performance. 

This strategy aims to effectively deal with large posterior uncertainty in higher dimensional problems and related over exploration behavior occurring in traditional methods. Since the algorithm is not relying on one global model, it may use different values for hyperparameters in the local models and hence greatly improve modeling capabilities of heterogeneous functions.


## Project Tasks

The project task was to implement TuRBO algorithm *and* integrate it into an existing package `BayesianOptimization.jl`. The author of this package and of the project proposal is my supervisor Johanni.

## The Challenge

Initially, I thought that given my background in math and sufficient Julia skills, working on the project tasks should be pretty straightforward. 

> I just need to invest time into: 
> 
> - surveying the field, learning about BO
> - studying the paper, where TuRBO was described
> - studying existing implementations of TuRBO in other languages
> - researching potential dependencies (implementations of GPs in Julia)
> - putting pieces together, testing, documenting code


There are a few comments I would like to make at this point. 

Firstly, studying existing implementations is not a good idea, unless their license permits to reuse implementation tricks *under the terms you agree to accept*. By learning and implementing some fairly obvious but special tricks, I had to restrict the license of my TuRBO implementation, which would otherwise not have been necessary.

Secondly, there is one important aspect missing. It should *integrate* well with existing software. And this is frankly the challenge because suddenly the task has changed into designing a modular, extendable system in which we can implement both TuRBO algorithm and traditional BO methods. The missing aspect I want to emphasize, is an *overarching software engineering effort*, which for me was a great learning opportunity.


## The Results

It turns out that the software engineering part in this case is highly mathematical in its nature. If you think about it, the question really is a question of a good abstract definition of a BO-like algorithm.

The definition should be general enough to cover all BO-like algorithms but also concrete enough to be useful for implementation. This is in a complete analogy to math, where we wish to define objects that are general enough to include interesting examples but at the same time specific enough so that we can prove strong results[^1].

This abstract view is very compatible with the way we can write generic code in Julia. The idea is to define abstract data types and formulate a prototypical BO-like algorithm using them. Each specific BO-like algorithm will provide its own concrete data types that are subtypes of the respective abstract types. Concrete building blocks are then correctly merged with the generic prototypical algorithm via multiple dispatch. The analogy of multiple dispatch in math is that valid claims for general objects still hold for more specific objects of the same kind and by concretizing we can get stronger results, i.e., exploit additional structure arising from extra assumptions.

The above structure results in code reuse and follows dependence inversion principle, where high level algorithms depend on abstractions that low-level modules implement.

One way we can abstractly formalize optimization algorithms is to think of them as decision making algorithms under uncertainty. Each time an optimization algorithm proposes to evaluate the objective function at some point, it has to make a decision based on some previously observed and possibly not too many evaluations. For details on this formalization, please see the first chapter in (\cite{garnett}).

The resulting project structure consists of the following packages, all available in [JuliaBayesianOptimization](https://github.com/JuliaBayesianOptimization) organization on github.

- `AbstractBayesianOptimization.jl`
- `BayesianOptimization.jl`
- `TrustRegionBayesianOptimization.jl`

In the following subsections, I will shortly describe them in more detail.

### AbstractBayesianOptimization.jl


This package formulates a prototypical BO algorithm using abstract building blocks. Currently it only supports gradient free optimization.

In principle, up to some details, it implements the following loop

```
while run_optimization(dsm, oh)
    xs = next_batch!(policy, dsm, oh)
    ys = evaluate_objective!(oh, xs)
    update!(dsm, oh, xs, ys)
end
```

where `dsm` is an `AbstractDecisionSupportModel`, `oh` is an `AbstractOptimizationHelper` and `policy` is an `AbstractPolicy`.

The role of a decision support model is to aggregate evaluations into some kind of model that is used by a policy to decide where to sample next. An optimization helper is logging best observed optimizer, history, overall time and normalizes the problem, i.e., transforms it into a maximization problem on a unit cube.

By calling `next_batch!`, we get an array of points at which we evaluate the objective function next by calling `evaluate_objective!`. A decision support model is subsequently updated with the new evaluations by calling `update!`. The loop runs as long as `run_optimization(dsm, oh)` returns true, e.g., as long as there is sufficient evaluation budget left.

A decision support model and a policy can maintain some state, but only related to their roles. If they need to access some optimization statistics, e.g., history, they do so by using an optimization helper.

`AbstractBayesianOptimization.jl` comes with an `OptimizationHelper`, a subtype of `AbstractOptimizationHelper` that provides basic utilities for defining unconstrained problems and logging progress.

The `AbstractBayesianOptimization.jl` package formalizes what a prototypical BO-like algorithm is, normalizes input and provides a unified way for getting outputs.

Current status:
- documented in code
- well tested 

Possible improvements:
- register package
- add documentation website
- add github actions
- add support for constrained problems
  - currently supports just gradient free optimization for unconstrained problems
- add verbosity levels for logging
- add parallel objective evaluation
- consider developing the "ask-tell" interface, for more see [here](https://github.com/jbrea/BayesianOptimization.jl/issues/37)

###  BayesianOptimization.jl

This package is refactoring current `BayesianOptimization.jl` package to fit the abstract framework developed in `AbstractBayesianOptimization.jl`. 

Obviously, it depends on  `AbstractBayesianOptimization.jl` and defines `BasicGP` decision support model and various policies.

`BasicGP` maintains a global Gaussian process surrogate.

Policies decide where to sample next by maximizing a so called acquisition function. An acquisition function is a function defined on the domain of the objective function and uses information from `BasicGP` to quantify how useful an evaluation at a point in the domain could be. 

In sense, we are searching for an optimal compromise between exploration and exploitation, i.e., sampling where we are uncertain about the objective and sampling where we expect high objective values, respectively.
These are the two opposing concerns that drive the progress of global optimization. 

Acquisition functions are usually cheap to evaluate and to optimize and don't evaluate the objective function at all.

Available acquisition functions:
- expected improvement
- max mean
- mutual information
- probability of improvement
- Thompson sampling
- upper confidence bound

Current status: 
- partly documented
- not well tested but works on examples

Possible improvements:
- add tests
- add documentation & documentation website
- add github actions
- register package
- depend on a more efficient optimization package for optimizing acquisition functions
- improve defaults for optimizing acquisition functions
- add new acquisition functions
- add `Base.show` methods
- add verbosity levels for logging
- integrate logging with `AbstractBayesianOptimization.jl`

### TrustRegionBayesianOptimization.jl

This package implements the TuRBO algorithm. It builds on  `AbstractBayesianOptimization.jl` and defines a decision support model `Turbo` and a policy `TurboPolicy`.

Since there are too many details to TuRBO algorithm, I refer the reader to the original source, (\cite{Turbo}), and the actual implementation.

Unfortunately, for the reasons already mentioned, it carries a restrictive license.

Current status:
- provides basic documentation
- not well tested but works on examples

Possible improvements:
- register package
- add tests
- add documentation & documentation website
- add github actions
- add benchmarks and test against a BOTorch implementation, available [here](https://botorch.org/tutorials/turbo_1)
- add benchmarks and test against the official Python [code-release](https://github.com/uber-research/TuRBO/tree/master)

## Moving GP surrogate where it belongs

TuRBO as well as traditional BO methods depend crucially on GPs with hyperparameter optimization. Fortunately, there is a package `AbstractGPs.jl` that provides tools for working with GPs. 

Initially, TuRBO relied on `AbstractGPs.jl` directly and included some utilities for working with `AbstractGPs.jl`. These utilities however could be useful for other projects as well, and so we thought we need to move our GP surrogate out of the project.

The package `Surrogates.jl` formalizes via the `AbstractSurrogate` interface what a deterministic surrogate is. As we would like to integrate our GP surrogate into an existing system of surrogates, we needed to rework the formalization of a surrogate to include stochastic surrogates too.

Currently this formalization is still in progress, happening inside `SurrogatesBase.jl` available in  [SciML organization](https://github.com/SciML/SurrogatesBase.jl) on github.

At the moment, the reworked version of `BayesianOptimization.jl` depends on our version of `SurrogatesAbstractGPs.jl` on branch `at_point` (available [here](https://github.com/JuliaBayesianOptimization/SurrogatesAbstractGPs.jl)) that refactors existing `SurrogatesAbstractGPs.jl` from SciML org, available [here](https://github.com/SciML/Surrogates.jl/tree/e6aa022e612ac57228506e625c662438d385e69d/lib/SurrogatesAbstractGPs).
Our version of `SurrogatesAbstractGPs.jl` on branch `at_point` depends on our version of `SurrogatesBase.jl` on branch `at_point` available [here](https://github.com/samuelbelko/SurrogatesBase.jl).

`TrustRegionBayesianOptimization.jl` depends on our version of `SurrogatesAbstractGPs.jl` too, but on branch `param-abstract-type`.
Our version of `SurrogatesAbstractGPs.jl` on branch `param-abstract-type` depends on `SurrogatesBase.jl` on branch `param-abstract-type`.

This maximally confusing dependence on branches allows me to develop the main software further and just reflects the work-in-progress nature of the project.

## Summary

GSoC'23 has been a super cool opportunity to learn some hands-on development in Julia. In particular I learned a great deal about developing mathematical software packages. To my own surprise, the structure behind a generic Julia project is in some ways analogous to how mathematical theories are build up.

## Acknowledgements

I would like to thank Johanni Brea for interesting discussions, feedback and many valuable ideas. The book (\cite{garnett}) has been a very useful source of conceptual ideas and explanations and so I thank the author, Roman Garnett, for his efforts. And of course many thanks to GSoC team at Google.

## References

- \biblabel{tut}{A Tutorial on Bayesian Optimization} Peter I. Frazier.  A Tutorial on Bayesian Optimization. 2018. [arXiv](https://arxiv.org/abs/1807.02811)
- \biblabel{garnett}{Bayesian Optimization} Roman Garnett, Bayesian Optimization. 2023. Cambridge University Press. [bayesoptbook.com](https://bayesoptbook.com/)
- \biblabel{Turbo}{Scalable Global Optimization via Local Bayesian Optimization} David Eriksson, Michael Pearce, Jacob Gardner, Ryan D. Turner, Matthias Poloczek. Scalable Global Optimization via Local Bayesian Optimization. 2019. [NeurIPS](https://proceedings.neurips.cc/paper/2019/hash/6c990b7aca7bc7058f5e98ea909e924b-Abstract.html)
<!-- - \biblabel{hands-on}{Hands-On Design Patterns and Best Practices with Julia} Tom Kwong. Hands-On Design Patterns and Best Practices with Julia. 2020. Packt. [Packt](https://www.packtpub.com/product/hands-on-design-patterns-and-best-practices-with-julia/9781838648817)
 -->

[^1]: An idea often emphasized by professor Noam Berger in his classes.