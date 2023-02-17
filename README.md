# image_reconstrcution
A novel strategy to shredded image reconstruction via Markov chain Monte Carlo method.

## Background
Ever since the paper shredder was invented, people have worked on retrieving the original document from the pieces. The unshredding problem can be viewed as a special case of the general jigsaw reconstruction problem. However, the majority of early methods focus entirely on the shape information of the pieces, such as the reconstruction of hand-torn documents, which are generally quite computationally expensive. In this paper, we utilize the visual information of the pieces with the focus on automatic reconstruction of cross shredded documents. As opposed to the jigsaw formulation, this problem has no edge shape information to use. We employ the Metropolis-Hastings algorithm of a Markov chain Monte Carlo method with a cost function to effectively determine the status of two pairwise shredders sequentially. The proposed method is capable of solving over 150 instances. In addition, multi-pages with various types (printed, handwriting and image) of documents can be recovered. The optimization strategy is also provided to speed the reconstruction process.  Finally, the current bottlenecks in this pipeline are identified and solutions are proposed.

## Methodology
```math
	\item \textbf{step 1} choose two patches with $p = \frac{1}{n}$, where $n$ is the total number of shredders.
  \item \textbf{step 2} for $32$ possible rotations (including the current one), calculate $E_r = \sum_r \be_i^\top \be_i, r = 1, \ldots, 32$.
  \item  \textbf{step 3} choose $S^{th}$ proposal with $P_r(\tilde{Z}_c \rightarrow \tilde{Z}_s) = \frac{E_s^{-1}}{\sum_{r = 1 \& r\neq c}^{32} E_r^{-1}}$.
  \item \textbf{step 4} MCMC algorithm, choose the proposal or not based on $min\Big(1, \frac{P_r(\tilde{Z}_s \rightarrow \tilde{Z}_c)}{P_r(\tilde{Z}_c \rightarrow \tilde{Z}_s)} \cdot \frac{\pi(\tilde{Z}_s|\by)}{\pi(\tilde{Z}_c|\by)}\Big)$, where $\pi(Z|\by) = \frac{\pi(Z \cdot \by)}{\pi(\by)} = \frac{\pi(Z) \cdot \pi(\by|Z) } {\pi(\by)}$. After validation, the closest form of $\pi$ is the Gamma distribution. Then the MCMC algorithm becomes \small
 {$$min\bigg(1, \frac{E_c^{-1} \cdot \sum_{r \neq c}^{32} E_r^{-1}} {E_s^{-1} \cdot \sum_{r \neq s}^{32} E_r^{-1}} \cdot \exp \Big(\sum \log(Gamma_{\alpha, \beta} (\be_i^2|\tilde{Z}_s)) - \sum \log(Gamma_{\alpha, \beta} (\be_i^2|\tilde{Z}_c)) \Big)  \bigg)$$} \normalsize
 \item \textbf{step 5} If the proposal accepted, update the parameter of $Gamma$ distribution.
  \item \textbf{step 6} After some burning time, if the status doesn't change or alomost stationary. Take it as the final ststus.
```

