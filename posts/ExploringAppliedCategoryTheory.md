@def title = "Exploring Applied Category Theory"

# Exploring Applied Category Theory 

By **Samuel Belko**, published on May 17, 2025.

In this post, I’m sharing my journey exploring applied category theory (ACT) and some resources I’ve found valuable.

My interest in ACT started in 2020/2021 when I got genuinely excited about functional 
programming. I don't remember exactly where I heard about ACT first, likely on HackerNews. I started following the online lectures [Programming with Categories](http://www.brendanfong.com/programmingcats.html) 
by Brendan Fong, Bartosz Milewski, and David Spivak, given at MIT in 2019. This course is interesting because it 
was designed by people with a profound ACT knowledge. I consider the course to be a course 
on ACT *with application* to functional programming. By design of the course, you might get 
excited about ACT itself, and try to understand how these concepts instantiate in other 
domains too. At least, this was my experience. 

Next, I looked at the 
[Seven Sketches in Compositionality: An Invitation to Applied Category Theory](https://arxiv.org/abs/1803.05316) book by Brendan Fong and David Spivak. The idea was to 
 work through the book with some friends, parallel to our university
lectures, with the goal of expanding our knowledge. Each chapter of the book begins 
slowly, e.g., introducing notions of a set and a function, but then quickly escalates into 
advanced concepts. To be fair, the book aims to give people a 
glimpse of what compositionality means in some concrete examples. It's not a traditional 
textbook course and we somehow didn't manage to go deep into the hard concepts.

I also explored some introductory papers, such as [What is Applied Category Theory?](https://arxiv.org/abs/1809.05923) by Tai-Danae Bradley, and [Physics, Topology, Logic and Computation: A Rosetta Stone](https://math.ucr.edu/home/baez/rosetta.pdf) by John Baez. 
These papers illustrate the connection between a diagramatic language for reasoning about 
processes and their formalization as categorical structures. It's interesting to 
see how certain commutative diagrams translate into rules how we can manipulate a string 
diagram without changing its meaning. Speaking of string diagrams, there is a Julia package for working with them called [Catlab.jl](https://algebraicjulia.github.io/Catlab.jl/stable/).

Due to concurrent commitments, I stopped getting deeper into ACT, until in 2024, when I started writing my master's thesis on design optimization of hybrid railway vehicle. It turned out that ACT concepts were relevant to the problem I was solving in my thesis! To be more precise, it is the theory of [Co-Design](https://arxiv.org/abs/1512.08055), developed by Andrea Censi, which helped me structure the problem I was working on. I started looking into the [draft of lecture notes](https://storage.zuper.ai/sync/ACT4E/ACT4E/alphubel-prod/build/last/build-public/ACT4E-public.pdf) for a course called [Applied Compositional Thinking for Engineers](https://applied-compositional-thinking.engineering), held at ETH in 2020. These lecture notes still have some rough edges but are truly beautifully written. There are so many good motivations for introducing abstract concepts, e.g., illustrating composition in categories as composition of chords with compatible plugs. Or for instance, how ACT differs from graph theory, in the way ACT studies situations where certain morphisms coincide, expressed using commutative diagrams. I think it is a solid textbook for starting with ACT, and a great companion for the [Seven Sketches in Compositionality](https://arxiv.org/abs/1803.05316) book.

I had a look at the [Programming with Categories lecture notes](http://www.brendanfong.com/programmingcats_files/cats4progs-DRAFT.pdf) again. Finally, I understood some of the categorical concepts much deeper. For instance, I dig deeper into monads and out of sudden, I realized that I am actually using a monad design pattern in my own code written as part of the master's thesis. See my post [Maybe Types in an Input for an Optimization Software](../MaybeTypesInAnInputForAnOptimizationSoftware) for more details. To understand monads, I recommend watching [Brian Beckman: Don't fear the Monad](https://www.youtube.com/watch?v=ZhuHCtR3xq8).

It's incredible, how ACT concepts are present all around us, but unless we know the right words, we will never notice the overaching theme. 
For instance, Lucas DiCioccio’s blog post [On Dualities](https://dicioccio.fr/on-dualities.html) discusses the dual roles of product and sum types in API design, and how they can be nested or flattened.

Well, there is still a lot to explore. I find ACT exciting because it is quite challenging to wrap your mind around it. I'm a very visual person, and I find it natural to reason in diagrams and sketches. ACT provides a formalism to all sorts of visual reasoning tools, and a lot more, e.g., giving meaning to diagrams via functorial semantics. My hypothesis is that people with an interest in ACT are also quite visual learners, and in fact all the materials I have seen so far, had high standards for explaining concepts visually.

Looking a bit into the future, at some point I would like to read the following books:
- [Picturing Quantum Processes](https://www.cambridge.org/core/books/picturing-quantum-processes/1119568B3101F3A685BE832FEEC53E52)
- [The Joy of Abstraction](https://www.cambridge.org/core/books/joy-of-abstraction/00D9AFD3046A406CB85D1AFF5450E657)

I hope I could give you some links and share a bit of my story with ACT. Thanks for reading!