@def title = "Maybe Types in an Input for an Optimization Software"

# Maybe Types in an Input for an Optimization Software

By **Samuel Belko**, published on April 26, 2025.

In this post, I would like to discuss how a type system can help us deal with complexity of implementing a generic optimization application. I illustrate the ideas on a concrete example, developed in my master thesis on design optimization of a hybrid railway vehicle.

A hybrid vehicle uses some combination of different power components, combining diesel engines, batteries, fuel cells and electrical network. There are vehicles that are just electric or use only batteries or use a combination of batteries with an electrical network, or combine diesel engines with batteries and an electrical network etc.

A user can choose which components a vehicle contains, and based on that, a corresponding optimization model is generated and solved. As the same models of power components are used irrespectable of in which combination they appear, we can write a generic software that will combine the models of available components automatically, to create a corresponding scenario model.
The approach I would like to discuss relies on [maybe data types](https://en.wikipedia.org/wiki/Option_type), modelling availabilities of components in an input. If a component is not available in an input configuration, it should also not be avalable in the scenario model and also not be available in the returned solution. Hence, the idea is to propage the configuration definition, encoded via a type system, throughout all stages of the execution. This design pattern closely resambles a [maybe monad](https://en.wikipedia.org/wiki/Monad_(functional_programming)#An_example:_Maybe), where some functions may fail, and their failure is propagated thoughout execution. In our use case for a maybe monad, only the user "can fail" to provide a component, and the intermediate steps cannot fail by themselves.

In the following, let $A$ and $B$ be types. We denote a sum type of $A$ and $B$ by $A+B$, and a product type of $A$ and $B$ by $A \times B$. If you imagine a type as a set containing all instances of that type, then you can think of the sum type $A+B$ as a [disjoint union](https://en.wikipedia.org/wiki/Disjoint_union) of $A$ and $B$, and of the product type $A \times B$ as a set product of $A$ and $B$. Sum types are generally known under the name [enums](https://en.wikipedia.org/wiki/Enumerated_type), and product types under the names tuples, records or structs. 

The important high level concept is the following. A function defined on a sum type $A+B$, requires *either* an instance of type $A$, *or* an instance of type $B$. A function defined on a product type $A \times B$, requires *both* instances, one of type $A$ and one of type $B.$ Somewhat dually, the implementation of a function defined on a sum type $A+B$, has to deal with *both* cases, as it doesn't know, if a caller passed an instance of type $A$ or an instance of type $B$. And the implementation of a function defined on a product type $A \times B$, can choose to discart a part of the input. For example, use the input instance of type $A \times B$ for projecting out an instance of type $A$, and then call some function defined on type $A$.

Furthermore, we define a *maybe type* of a type $A$ as $A + 1$, where $1$ is a singleton type. A singleton type has only one instance. Hence, an instance of type $A+1$ is *either* an instance of type $A$, *or* the only instance of type $1$.

In our case, we define a singleton type called $\text{NotAvailable}$. For a type $A$, we construct a new type $A + \text{NotAvailable}$, creating an option to not specify a component related data.

Let $ C^\text{input}_i$ denote the input type for a component $i$, $C^\text{problem}_i$ denote the type managing the model of a component $i$ and $C^\text{solution}_i$ denote the type representing the part of a solution corresponding to a component $i$.
On a very high level, omitting further inputs, we obtain the following type definitions for my implementation,
\begin{align*}
    \text{Scenario} &= \prod_{i} ( C^\text{input}_i + \text{NotAvailable}) \\
    \text{Problem} &= \prod_{i} ( C^\text{problem}_i + \text{NotAvailable}) \\
    \text{Solution} &= \prod_{i} ( C^\text{solution}_i + \text{NotAvailable}).
\end{align*}
The interpretation is that a scenario manages inputs for all components, and if a component is not available, then we pass a special instance of type $\text{NotAvailable}$. To obtain an optimization problem, for each available component, we generate a model and collect them inside a $\text{Problem}$ instance. We propagate the component availability information in a obvious way, if a component was not available in a scenario, it is also not avaible in a $\text{Problem}$ instance. After we have generated a $\text{Problem}$ instance, we can try to solve it. If we succeed, we return an instance of a $\text{Solution}$ type. We propagate the component availability information as before, mapping not available component models to not available component solutions.

For example, in a programming language Julia, we could define abstract data types,
```Julia
abstract type AbstractComponent end
abstract type AbstractComponentProblem end
abstract type AbstractComponentSolution end
```
and for each component $i$, set $ C^\text{input}_i$ to be a subtype of `AbstractComponent`, $C^\text{problem}_i $ to be a subtype of `AbstractComponentProblem` and $C^\text{solution}_i$ to be a subtype of `AbstractComponentSolution`. Then, we introduce the singelton type $\text{NotAvailable}$ as follows.
```Julia
struct NotAvailable end
```
Below, we define a parametric type `MaybeAvailable{T}` mapping a type $T$, which is modeling either a component input or a component model or a component solution, into a type $T + \text{NotAvailable}$.
```Julia
const MaybeAvailable{T} = Union{
    NotAvailable,T
} where {T<:Union{AbstractComponent,AbstractComponentProblem,AbstractComponentSolution}}
```
We define a useful utility function `isavailable` for checking whether an instance of type $T +  \text{NotAvailable}$ is actually available or not.
```Julia 
isavailable(x::MaybeAvailable) = !isa(x, NotAvailable)
```
In the implementation of the optimization software, I repeatedly rely on the following pattern, to realize the propagation mechanism.
```Julia 
maybe_processed_component = if isavailable(maybe_component)
    # process component
else
    NotAvailable()
end
```
The above `if else` statement is matching `maybe_component` against the two cases of the disjoint union type. Either `maybe_component` is available, and we proceed to the next stage, or `maybe_component` is not available, and we return an instance of type $\text{NotAvailable}$.

Now, this is nothing else than an ad hoc definition of a composition in a maybe monad. But, my implementation is not composing pure functions, and so the monadic pattern is just a rough analogy. Overall, we can trace back the structural ideas to functional programming, but implemented in a multi-paradigm language.

What I would like to especially highlight, is that the maybe monad idea was repurpused to help us deal with complexity arising in having different components being available in the input. Yet, still, the same principles proved useful, even though the motivation for a maybe monad is to model partial functions, which may fail during execution. I find the idea very elegant, to "compose" the user providing an input with the program, and model the user as a "function which may fail" to provide input data. This deliberate failure of the user  is then propagated through the program in a consistent manner.

If you like to learn more about the math behind functional programming, I can recommend reading the excellent lecture notes for the course [Programming with Categories](http://www.brendanfong.com/programmingcats.html), held at MIT in 2020.

I hope this blog post illustrated an example use of maybe data types in an industrial software application, and that you have learned something new.