# Resources for Learning Julia

By **Samuel Belko**, published on May 28, 2024.

I would like to share some resources that helped me improve my coding skills and the overall experience with Julia. From what I know so far, it is a good idea to invest time into understanding the type system and multiple dispatch, since it is the way idiomatic & performant Julia code is written. Below I collect some topics I found useful, and learned from the materials.

- [SciML Book: optimizing serial code](https://book.sciml.ai/notes/02-Optimizing_Serial_Code/)
   - memory related speedups
    - avoid cache misses
    - column major indexing of matrices
    - stack vs heap
    - mutating a cache variable in place to avoid new allocations
    - using views instead of slicing an array
    - fusing loop operations with broadcasting to avoid allocations of temporary variables
  - function specialization related speedups
    - type inference inside functions & type stability
    - compiler is building a different function for different combinations of input types that is specialized and only works for that type combination
    - type information is compile time information and values are runtime information, types needs to provide enough information to the compiler to generate efficient, specialized lowered code
    - *multiple dispatch as a natural extension of just-in-time compilation*
    - parametric types can create a family of `isbits` types
    - abstract types don't help the compiler (but are useful for steering multiple dispatch)
    - function barriers for dealing with type instability (one dynamic dispatch call after which only static dispatch calls follow)
- [Hands-On Design Patterns and Best Practices with Julia](https://www.oreilly.com/library/view/hands-on-design-patterns/9781838648817/)
  - semantic versioning
  - dependency inversion principle
  - function forwarding & extending types by wrapping them
  - rules for *automatic* conversion between data types
  - array of structs vs struct of arrays
  - the holy traits pattern, singleton type dispatch
  - type piracy anti-pattern (extending a third-party function without using own types in any of the function arguments)
- [Modern Julia Workflows](https://modernjuliaworkflows.github.io/)
  - [development workflow](https://modernjuliaworkflows.github.io/writing/#development_workflow)
  - [REPL startup config](https://modernjuliaworkflows.github.io/writing/#configuration)
  - [code quality](https://modernjuliaworkflows.github.io/sharing/#code_quality)