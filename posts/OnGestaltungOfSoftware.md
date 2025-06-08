@def title = "On «Gestaltung» of Software"

# On «Gestaltung» of Software

By **Samuel Belko**, published on June 8, 2025.

In this post, I would like to reflect on the content of the book \cite{SWE}, which changed the way I think about programming.

The title uses the German word *Gestaltung*, which unfortunately does not have a direct one-word translation in English. A first-order approximation might be design, but to me, Gestaltung conveys something more authentic, combining intention, meaning, aesthetics, and function. It is a deliberate creative process of organizing elements into a coherent whole. This is now how I think about programming.

At a high level, the book \cite{SWE} draws a distinction between *hacking* new features and intentionally designing and redesigning abstractions. For instance, there is a chapter on writing comments, where the recommended approach is to first iterate on the comments that specify the main interfaces of classes and public methods. Once the design feels just right, we proceed with the concrete implementation. The focus is on iterating over abstractions, not the implementation.

For me personally, concept formation is the most interesting part of programming. Possibly, it is my formal math training that makes me genuinely appreciate clean problem decompositions. I consider programming as solving a modeling problem, in which one tries to find the right words to clearly describe a complex system. In the description, everything should have a place, and everything should be in its place.

Another piece of advice I resonated with was to *design it twice*. The idea is to explore as many varied designs as possible and evaluate their tradeoffs before settling on one. The guiding principle is to choose a design that makes higher-level software easier to use, even at the cost of a more involved implementation. The codebase should be user-friendly at every level of abstraction, making it enjoyable to work with. Especially, since most lines of code written are extensions of existing software.

The author makes a thought-provoking remark about how young, smart people often have a hard time embracing the concept of *designing it twice*. As the author argues, many grow up accustomed to their first ideas being sufficient to earn a good grade.

In conclusion, after reading the book and reflecting on my own programming experience, I’ve come to believe that the challenges of clean software design are rooted in the clear communication of complex ideas. I used to think of programming as a discipline primarily concerned with algorithms, but the book \cite{SWE} offered a new perspective, placing *interfaces* at the center of the discussion and implementation at the periphery.

## References

- \biblabel{SWE}{A Philosophy of Software Design} John Ousterhout. A Philosophy of Software Design. 2018