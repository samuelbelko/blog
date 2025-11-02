@def title = "Modularity Requires Glue"

# Modularity Requires Glue

By **Samuel Belko**, published on November 2, 2025.

In [*Why functional programming matters*](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf) (1989), John Hughes argues that functional programming is powerful because it enables a high degree of modularity. Modularity demands abstract glue, which in this case is delivered by higher-order functions and lazy evaluation.

For instance, consider the higher-order function `map` taking `f: A -> B`, `List[A]` and returning `List[B]` by applying `f` element-wise. It serves as a specialized kind of glue, useful when a task can be decomposed into applying `f` across the elements of `List[A]`. As the paper suggests, each new data type should be equipped with glue-like functionalities that allow us to use it ergonomically. The above example with a `List` supporting a `map` can be generalized to algebraic data types featuring higher-order functions for transformation, reduction, and generation from seeds[^1].

This notion of glue extends beyond functional programming. In other paradigms, we also pursue modularity, and hence invent new ways to bind parts together.

For instance, Julia’s multiple dispatch chooses an implementation based on input types, making it a form of contextual, recursive glue for composing behavior. This glue is context-dependent, since it selects the best matching implementation from the methods available in scope[^2].

Similarly, inheritance in object oriented programming is a way to glue changes onto a base class. The glue in this case is merging both data and behavior. Even if the parts are simple, the resulting class might be too complex to work with. It is not as straightforward to decompose such emergent complexity, compared to pattern matching on algebraic data types.

I find the idea of investigating the glue within systems intriguing, as it shifts the perspective from individual parts to the relations between them. Since most complex artifacts are modular, there is a lot of glue to see if we search for it.


[^1]: For example, a [catamorphism](https://en.wikipedia.org/wiki/Catamorphism) generalizes reduction (folds) of recursive data types.
[^2]: This makes some people uneasy [HN](https://news.ycombinator.com/reply?id=45768702&goto=item%3Fid%3D45711003%2345768702).