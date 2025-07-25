@def title = "A Note on Scalarization Error in Lexicographic Optimization"

# A Note on Scalarization Error in Lexicographic Optimization

By **Samuel Belko**, published on July 25, 2025.

In this post, I describe an insight I realized in my master's thesis on design optimization of hybrid railway vehicles.

For brevity, we consider a one-dimensional, unconstrained setting.
Let $f_1, f_2 : \mathbb{R} \to \mathbb{R}$ and suppose that for
some constants $L,U$ and all $x$ holds $ L \leq f_2(x) \leq U$.

Consider a lexicographic problem

$$ \min_{x} \; f_2(x)  \; \text{ subject to } \; f_1(x) \leq \min_{x'} f_1(x'). $$

For some $K>0$, consider a scalarized version of (1) 

$$ \min_{x} \; f_1(x) + K f_2(x). $$

Furthermore, consider an optimizer $x^*_{\text{lexi}}$ of (1) and $x^*_{\text{scal}}$ of (2).
By the lexicographic constraint in problem (1), we get

$$ f_1(x^*_{\text{lexi}} ) \leq f_1(x^*_{\text{scal}}).$$

Optimality of $x^*_{\text{scal}}$ implies

$$ f_1(x^*_{\text{scal}} ) + K f_2 (x^*_{\text{scal}}) \leq  f_1(x^*_{\text{lexi}} ) + K f_2 (x^*_{\text{lexi}}). $$

Hence, we obtain that

$$ 0 \leq f_1(x^*_{\text{scal}}) - f_1(x^*_{\text{lexi}} ) \leq K f_2 (x^*_{\text{lexi}}) - K f_2 (x^*_{\text{scal}}) \leq K ( U - L) $$

holds. In particular 

$$ f_2 (x^*_{\text{scal}}) \leq f_2 (x^*_{\text{lexi}})$$

follows.

In conclusion, by choosing $K$ sufficiently small, $f_1 (x^*_{\text{scal}}) $ and $f_1 ( x^*_{\text{lexi}}) $ can become arbitrarily close. Irrespective of the choice of $K$, 
$f_2(x^*_{\text{scal}})$ is always at least as good as $f_2( x^*_{\text{lexi}})$.

The above insight is simple, yet it proved to be useful for greatly speeding up solving (1) by merging objectives and solving (2) instead. Though brief, I hope this this post sheds some light on scalarization in the lexicographic setting.
