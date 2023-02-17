# image_reconstrcution
A novel strategy to shredded image reconstruction via Markov chain Monte Carlo method.

## Background
Ever since the paper shredder was invented, people have worked on retrieving the original document from the pieces. The unshredding problem can be viewed as a special case of the general jigsaw reconstruction problem. However, the majority of early methods focus entirely on the shape information of the pieces, such as the reconstruction of hand-torn documents, which are generally quite computationally expensive. In this paper, we utilize the visual information of the pieces with the focus on automatic reconstruction of cross shredded documents. As opposed to the jigsaw formulation, this problem has no edge shape information to use. We employ the Metropolis-Hastings algorithm of a Markov chain Monte Carlo method with a cost function to effectively determine the status of two pairwise shredders sequentially. The proposed method is capable of solving over 150 instances. In addition, multi-pages with various types (printed, handwriting and image) of documents can be recovered. The optimization strategy is also provided to speed the reconstruction process.  Finally, the current bottlenecks in this pipeline are identified and solutions are proposed.

## Methodology
$\textbf{step 1}$ choose two patches with $p = \frac{1}{n}$, where $n$ is the total number of shredders.

$\textbf{step 2}$ for $32$ possible rotations (including the current one), calculate $E_r = \sum_r e_i^\top e_i, r = 1, \ldots, 32$.

$\textbf{step 3}$ choose $S^{th}$ proposal with 
```math
P_r(\tilde{Z}_c \rightarrow \tilde{Z}_s) = \frac{E_s^{-1}}{\sum E_r^{-1}}.
```
$\textbf{step 4}$ MCMC algorithm, choose the proposal or not based on
```math
$min\Big(1, \frac{P_r(\tilde{Z}_s \rightarrow \tilde{Z}_c)}{P_r(\tilde{Z}_c \rightarrow \tilde{Z}_s)} \cdot \frac{\pi(\tilde{Z}_s|y)}{\pi(\tilde{Z}_c|y)}\Big)$
```
where $\pi(Z|y) = \frac{\pi(Z \cdot y)}{\pi(y)} = \frac{\pi(Z) \cdot \pi(y|Z) } {\pi(y)}$.
After validation, the closest form of $\pi$ is the Gamma distribution. Then the MCMC algorithm becomes
```math
 {$$min\bigg(1, \frac{E_c^{-1} \cdot \sum_{r \neq c} E_r^{-1}} {E_s^{-1} \cdot \sum_{r \neq s} E_r^{-1}} \cdot \exp \Big(\sum \log(\Gamma_{\alpha, \beta} (e_i^2|\tilde{Z}_s)) - \sum \log(\Gamma_{\alpha, \beta} (e_i^2|\tilde{Z}_c)) \Big)  \bigg)
 ```
 
 $\textbf{step 5}$ If the proposal accepted, update the parameter of $\Gamma$ distribution.
 
 $\textbf{step 6}$ After some burning time, if the status doesn't change or alomost stationary. Take it as the final ststus.

## Experiments results
![](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExOTZiMTU1Mjc3OWFiNjQxN2UxZDg3YjliYmNiNTNiMWRlOTdmMzg0NCZjdD1n/kU2Ji6KDfgz1BKVlxZ/giphy.gif)

The pet image is shredded to $10 \times 5$ patches with edge fixed. The animation of reconstruction process is shown above.

<p align="center">
  <img src="https://user-images.githubusercontent.com/60518209/219530257-e6f7ffe3-8e26-4508-b62c-fe15f7ecb751.png" width="430" />
  <img src="https://user-images.githubusercontent.com/60518209/219530272-8b965a5e-f570-413e-9461-dd9023ce9055.png" width="430" />
</p>

The document is shredded to $8 \times 6$ patches with edge fixed. The results are shown above.
