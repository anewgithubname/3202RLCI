* **explanation and better connections to background theory**

TBD

* **The concerns about Arguement 1 and 2 in the Introduction.**

We believe that the theory of adversarial games by Goodfellow et al (2014) is a misunderstanding of GANs, as it is well-known that there already exists an inconsistency which is the non-saturated trick. The main reason of this misunderstanding is that adversarial games require $p_g$ as a functional variable ( $p_g$ is expressed by a parametric neural network $G$). In proposition 1 by Goodfellow et al (2014), the optimal discriminator satisfies 

$$
D^\\ast(G, x) = \\frac{p\_{data}(x)}{p\_{data}(x)+p_g(x)}=\sigma[d^\ast(G, x)]
$$ 

where the small $d^\ast(G, x) = \log \frac{p_{data}(x)}{p_g(x)}$ is the logit output in our paper. Given this optimal $D^\ast(G, x)$, the Jensen-Shannon divergence (JSD) is expressed by (see Eq (4) or Eq (5) by Goodfellow et al (2014)),

$$
\begin{align*} 
C(G) &= \mathbb{E}\_{x\sim p\_{data}}\left[ \log D^\ast(G, x) \right] + \mathbb{E}\_{x\sim p\_g}\left[ \log (1-D^\ast(G, x)) \right] \\\\
&= \mathbb{E}\_{x\sim p\_{data}}\left[ \log \frac{p_{data}(x)}{p_{data}(x)+ p_g(x)} \right] + \mathbb{E}\_{x\sim p\_g}\left[ \log \frac{p\_g(x)}{p\_{data}(x)+ p_g(x)} \right] \\\\
& = \Big[KL(p\_{data}||\frac{p\_{data}+p_g}{2}) - \log(2)\Big] + \Big[KL(p\_g||\frac{p\_{data}+p_g}{2}) - \log(2)\Big]
\end{align*}
$$


we can see that the first term of JSD is dependent on $G$ and this JSD $C(G)$ is a function of $G$. However, in the practical algorithm, $D$ is trained only with samples, therefore the optimal discriminator should be $D^\ast(G_{\text{detach}}, x)$ which loses the density information of $p_g$ and this is the reason why we drop out the first term of JSD in practice. As you can see, the generator loss of vanilla GAN actually approximates the second term, 

$$
KL(p\_{g}||\frac{p\_{data}+p_g}{2}) - \log(2)=\mathbb{E}\_{x\sim p\_g}\left[ \log(1-D^\ast(G_{\text{detach}}, x)) \right]
$$

this is not JSD **(our argument 1)** but a KL divergence up to a constant and furthermore, this should not be viewed as a divergence minimization problem **(our argument 2)** because $D^\ast(G_{\text{detach}}, x)$ cannot capture the variability of $p_g$ such that the estimated KL divergence is not a function of $p_g$ (or its parametric model $G$). This can be intuitively understood by that the divergence does not change together with $p_g$ and when $p_g$ changes, we need to re-estimate the divergence using samples to update the discriminator. However, in a variational divergence minimization problem, we need $D^*(G, x)$ to construct a probability divergence such that this divergence is a function of $p_g$ as we explained it in section 4.2.


In two-sample divergence estimations, the estimated divergence is not really useful and the useful information comes from the discriminator $d(x)$ which approximates the optimal model $d^*(G_{\text{detach}}, x)$-- the log density ratio. Since $d(x)$ is only a function of sample $x$, therefore we can only compute its gradient w.r.t. $x$ which naturally comes with particle evolution with vector fields. 

The vanilla loss and non-saturated loss work because they just construct monotonically increasing function $h$ and the derivative $h'$ rescales the vector field of MonoFlow (vanilla loss can work in toy data sets if two distributions are close such that the estimated log ratio is close to zero where $h'$ deviates from zero). We also have a discussion in section 4.3, where we can use the approximated log density ratio $d(x)$ to recover JSD but minimizing it does not work. The reason is that the $h$ function of the whole JSD is not an increasing function. In Appendix B.2, we have also shown that GAN can work with an arbitrarily increasing function $h$ as long as its derivative deviates from zero when $d(x)$ is less than zero.

* **The concern about the choice of discriminators and the accuracy of density ratio estimation.**

For density ratio estimation, we follow the same argument by the conventions of divergence GANs, e.g., the logit output $d^*(G,x)$ is the log density ratio is a direct result of **Proposition 1** by Goodfellow et. al. (2014) as we explained in the previous question. 

Density ratio estimation via binary classifications is stable because the cross entropy is associated with Sigmoid activation where extreme values are bounded such that overflow risks in the computation are low. We also found gradient penalties are essential to avoid discriminator outputting 'nan' values in training GANs if the density ratio model is trained via the Donsker-Varadhan representation or the f-divergence representation of KL divergence, see Nowozin (2016) or Belghazi (2018). But this is not discussed in our paper, we focus on the aspect of generator losses and we want to correct the misunderstanding of GANs as a divergence minimization problem. It is known that divergence estimation is equivalent to density ratio estimations, we refer to Sugiyama (2012) for more details on density ratio estimation.

* **The ambiguity of the title**

Thanks for pointing it out, we will consider changing the title to divergence GANs. We previously understand 'GAN variants' as a subclass of GANs, and this might have ambiguity.

References
1. Goodfellow , et al. "Generative adversarial networks." NeurIPS (2014).\
2. Belghazi, Mohamed Ishmael, et al. "Mine: mutual information neural estimation." ICML (2018).\
3. Nowozin, Sebastian, Botond Cseke, and Ryota Tomioka. "f-gan: Training generative neural samplers using variational divergence minimization." NeurIPS (2016).\
4. Sugiyama  Masashi, Taiji Suzuki, and Takafumi Kanamori. Density ratio estimation in machine learning. Cambridge University Press (2012).\
