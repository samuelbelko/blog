@def title = "Are Mathematicians Software Engineers in Disguise?"

# Are Mathematicians Software Engineers in Disguise?

By **Samuel Belko**, published on April 6, 2025.

> Mathematics has always been about coming up with abstractions that enable us to think more efficiently, communicate precisely, solve hard problems, and reach a stable consensus as to whether our claims are justified. 
>  Jeremy Avigad, in [^1]

There is a striking similiarity between extending a complicated codebase and writing down  complicated mathematical theories. The developer and mathematician are both challenged to come up with a structure that a human mind can parse, and that encodes relations to already existing interfaces, we are building on top of. In programming we are always coding against 
some lower level interfaces or utilities from some libraries. Similarly in math, we import proven statements into our scope and reference them in our proofs.

In math, people often introduce definitions that generalize ideas and allow for proving statements *at one fell swoop*. Arguably, creating abstraction hierarchies is the reason why can efficiently learn existing math.
Similarly, in programming, 
we introduce new interfaces that require some generic properties, that are sufficient for the implementation details.

Code refactoring with the goal of reducing dependencies and reusing code can be compared with decoposing a complicated proof into simpler lemmas, which do not need the access the full scope of the argument. In both cases, we are curving out some of the complexity into 
its own local scope.

When mathematicians introduce new notation and conventions, they are essentially 
*programming bottom up*, changing the langage 
to suit a problem, as Paul Graham expressed it in [Language and program evolve together](https://www.paulgraham.com/progbot.html). This is a common programming pattern, resulting 
in developing libraries, whose functionalities compose.

The analogies I touched upon originate from [^1], which discusses the future of mathematics, in light of formal verification methods.

I hope, you find the topic as intriguing as I did, when I saw the similarites for the first time.

[^1]: [Mathematics and the formal turn, 2023](https://arxiv.org/abs/2311.00007)