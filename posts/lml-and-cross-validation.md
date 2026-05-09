@def title = "Marginal likelihood is exhaustive leave-p-out cross-validation"

# Marginal likelihood is exhaustive leave-p-out cross-validation

By **Samuel Belko**, published on May 8, 2026.

In this post, I would like to highlight a connection between log marginal likelihood (LML) and cross-validation. In fact, log marginal likelihood is the same as exhaustive leave-p-out cross-validation, averaged across all train-test splits.

The derivation below follows the original proof from the paper \cite{mll-cv}.

Consider a statistical model specified by a prior distribution $ \pi(\theta)$ and a likelihood $f(y_{1:n} | \theta)$. The marginal likelihood is defined as

$$p(y_{1:n}) = \int f (y_{1:n} | \theta) \pi(\theta) d\theta$$

and quantifies the probability of the model generating the data.

By the chain rule of probability, for any permutation $\tilde{y}_{1:n}$ of $y_{1:n}$ entries, we have 

$$p(y_{1:n}) = \Pi_{i = 1}^n p(\tilde{y}_i | \tilde{y}_{1:i-1}).$$

@@colbox-green
_Example:_

There are $3!$ permutations of $y_{1:3} = (y_1, y_2, y_3)$ entries, and each one is inducing a decomposition:

\begin{align}
 p(y_1, y_2, y_3) &=  p(y_1) p(y_2 | y_1) p(y_3 | y_1, y_2) \\
                  &=  p(y_1) p(y_3 | y_1) p(y_2 | y_1, y_3) \\
                  &=  p(y_2) p(y_1 | y_2) p(y_3 | y_1, y_2) \\
                  &=  p(y_2) p(y_3 | y_2) p(y_1 | y_2, y_3) \\
                  &=  p(y_3) p(y_1 | y_3) p(y_2 | y_1, y_3) \\
                  &=  p(y_3) p(y_2 | y_3) p(y_1 | y_2, y_3) 
\end{align}
@@


Applying logarithm in (2), we get 

$$\log p(y_{1:n}) = \sum_{i = 1}^n \log p(\tilde{y}_i | \tilde{y}_{1:i-1}).$$

Since the decomposition holds for any permutation of $y_{1:n}$ entries, taking the arithmetic mean over all permutations $\{\tilde{y}_{1:n}^{j}\}_j$ yields

$$\log p(y_{1:n}) = \frac{1}{n!} \sum_{j=1}^{n!} \sum_{i = 1}^n \log p(\tilde{y}_i^j | \tilde{y}_{1:i-1}^j).$$

To make the connection with leave-p-out cross-validation apparent, we change the order of summation in (5). We group summands together that are conditioned on the same $y$ entries regardless of the order of entries we are conditioning on. For instance, $p(y_1 | y_2, y_3)$ and $p(y_1 | y_3, y_2)$ are in the same group. We call the $y$ entries that we are conditioning on within a group a training set, and denote it by $\mathcal{D}_g,$ for a group $g$. Furthermore, we sort the summation by the cardinality of $\mathcal{D}_g$.

@@colbox-green
_Example:_ 

Continuing with the previous example, each training set $\mathcal{D}_g$ has cardinality in $\{0,1,2\}$. After grouping and sorting, we obtain the following decomposition:

\begin{align}
\log p(y_1, y_2, y_3)
  &= \frac{1}{3}\bigl(
       \log p(y_1) + \log p(y_2) + \log p(y_3)
     \bigr)  \\
  &\quad + \frac{1}{6}\bigl(
       \log p(y_3|y_1) + \log p(y_2|y_1) + \log p(y_1|y_2)  \\
  &\qquad\qquad\quad
       + \log p(y_3|y_2) + \log p(y_2|y_3) + \log p(y_1|y_3)
     \bigr)  \\
  &\quad + \frac{1}{3}\bigl(
       \log p(y_2|y_1,y_3) + \log p(y_3|y_1,y_2) + \log p(y_1|y_2,y_3)
     \bigr)
\end{align}
@@

Note that each summand

$$\log p(y_i | \mathcal{D}_g) = \log \int f(y_i | \theta ) \pi(\theta | \mathcal{D}_g) d\theta$$

is quantifying the probability of the model generating $y_i$, conditioned on training data 
of the group $\mathcal{D}_g$. Hence, each summand in (5) corresponds to a cross-validation term evaluating a single test point out of $p = n - |\mathcal{D}_g|$ left out test points.

Therefore, we can interpret the sum (5) as an exhaustive leave-p-out cross-validation over all possible training sets and all possible left out points. This insight connects Empirical Bayes model selection with traditional cross-validation. See \cite{mll-cv} for details.

However, even with the interpretation of LML as an exhaustive cross-validation, LML is not a suitable proxy for model generalization, as \cite{model-selection} argues. In the decomposition (5), we can observe that there are many terms evaluating predictive performance of the model, when conditioned on few data points. In particular, $p(y_i)$ terms measure purely the fit of the prior. The issue is that generalization means finding a prior such that conditioning on some data, we obtain a posterior with a good predictive performance.

On the other hand, quite remarkably, in some models, we have an analytical formula for LML, i.e., we can efficiently evaluate an exhaustive leave-p-out cross-validation. For instance, this is the case in Gaussian Processes, assuming a Gaussian observation noise.

Thanks for reading!

## References

- \biblabel{mll-cv}{On the marginal likelihood and cross-validation} [On the marginal likelihood and cross-validation](https://academic.oup.com/biomet/article/107/2/489/5715611?login=false). E. Fong, C. C. Holmes. 2020
- \biblabel{model-selection}{Bayesian Model Selection, the Marginal Likelihood, and Generalization} [Bayesian Model Selection, the Marginal Likelihood, and Generalization](https://proceedings.mlr.press/v162/lotfi22a.html). S. Lotfi, P. Izmailov, G. Benton, M. Goldblum, A. G. Wilson. 2022