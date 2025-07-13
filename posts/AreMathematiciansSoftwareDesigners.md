@def title = "Are Mathematicians Software Designers in Disguise?"

# Are Mathematicians Software Designers in Disguise?

By **Samuel Belko**, published on April 6, 2025.

> Mathematics has always been about coming up with abstractions that enable us to think more efficiently, communicate precisely, solve hard problems, and reach a stable consensus as to whether our claims are justified.\
> -- Jeremy Avigad, in \cite{JA}

There is a striking similiarity between extending a complicated codebase and writing down  complicated mathematical theories. The developer and mathematician are both challenged to come up with a structure that a human mind can parse, and that encodes the relations to already existing interfaces, that they are building on top of. In programming, we are always coding against some lower level interfaces or utilities from some libraries. Similarly in math, we import proven statements into our scope and reference them in our proofs.

For instance, we can think of refactoring a complicated piece of code as an analogy for a decomposition of a complicated proof into simpler lemmas. Maybe we have identified some repeating constructions in some proofs, and we could encapsulate them into their own litte lemmas. These lemmas do not need to access the full scope of the logical argument and hopefully lower the overall cognitive load.

When mathematicians introduce new notation and conventions, they are essentially  *programming bottom up*, changing the language to suit a problem, as Paul Graham expressed it in \cite{Graham}. This is a common programming pattern, resulting in developing libraries, whose functionalities compose.

Furthermore, we can compare the advices of a well-known mathematician Paul Halmos on formulating clean theory expositions, collected in \cite{HA}, with advices for clean software design, discussed in a well-known reference \cite{SWE}.

Halmos mentions the following statements about how he is writing mathematics:

> The writer must anticipate and avoid the reader's difficulties.

> The best way to start writing, perhaps the only way, is to write in the spiral plan. According to the spiral plan the chapters get written and re-written in the order 1, 2, 1, 2, 3, 1, 2, 3, 4, etc. You think you know how to write Chapter 1, but after you've done it and gone on the Chapter 2, you'll realize that you could have done a better job on Chapter 2 if you had done Chapter 1 differently. There is no help for it but to go back, do Chapter 1 differently, do a better job on Chapter 2, and then dive into Chapter 3. And, of course, you know what will happen: Chapter 3 will show up the weaknesses of Chapters 1 and 2, and there is no help for it ... etc., etc., etc.

We can contrast the above with statements found in \cite{SWE}, such as:

> One of the most important goals of good design is for a system to be obvious.

> ... most software development projects today use an incremental approach such as agile development, in which the initial design focuses on a small subset of the overall functionality. This subset is designed, implemented, and then evaluated. Problems with the original design are discovered and corrected, then a few more features are designed, implemented and evaluated. Each iteration exposes problems with the existing design, which are fixed before the next set of features is designed.

We can see that both sources emphasize spending extra effort on redesigning the initial concepts so that at the end, the content is as easy as possible to follow. The contributions are not just a valid proof or a working piece of code, but something, which hopefully facilitates progress by providing a robust infrastructure, to build on top of.

The analogies I touched upon originate from \cite{JA}, which discusses the future of mathematics, in light of formal verification methods.

I hope, you find the topic as intriguing as I did, when I saw the similarities for the first time.

## References

- \biblabel{JA}{Mathematics and the formal turn} Jeremy Avigad. Mathematics and the formal turn. 2023. [arXiv](https://arxiv.org/abs/2311.00007)
- \biblabel{Graham}{Language and program evolve together} Paul Graham. Language and program evolve together. 1993. [blog post](https://www.paulgraham.com/progbot.html)
- \biblabel{HA}{How to Write Mathematics} Paul R. Halmos, How to Write Mathematics. [pdf](https://sites.math.washington.edu/~lind/Resources/Halmos.pdf)
- \biblabel{SWE}{A Philosophy of Software Design} John Ousterhout. A Philosophy of Software Design. 2018