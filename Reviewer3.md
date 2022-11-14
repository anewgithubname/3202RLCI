Dear reviewer,

Thanks for acknowledging the novelty and the significance of our paper. We have made the amendations following your advice. 

**Q1 Issues in the presentation and proofs of theoretical results**
>Why $\mathcal{T}$ must be a bijection?

As we have explained in Lemma 3.3, the density ratio is $d(x) = \mathcal{T}^{-1}[r(x)]$, hence $\mathcal{T}$ must be a bijection such that its 
inverse exists.

>Which precise space is $d$ optimized, and what are the hypotheses on $\phi$ and $\psi$?

There is no restriction for the space of $d$, it can be any measurable functions, 
e.g, a linear model or a neural net. We previously followed the convenctions of standard divergence GANs where $d$'s space is not particularly specified 
but now we added this explainations in the revision. 

>I do not understand how the proof of Theorem 3.1

Thanks for pointing it out. The proof might not be really straightforward for people who are not familiar with continuty equation. 
First, consider the Fokker Planck equation in Eq (3),

$$
\frac{\partial q\_t}{\partial t} = \text{div}(q\_t(\nabla_x \log q_t - \nabla_x \log p)) 
$$

this is the probability evoluation of Langevin dynamics or the log density ratio ODE of Eq(4) where $h$ is an identity mapping. 
The equilibrium is achieved if the probabilty current (flux) $q_t(\nabla_x \log q_t - \nabla_x \log p)$ is zero, 
meaning there is no net flow and $\frac{\partial q_t}{\partial t} = 0 $. Solving the differential equation $q_t(\nabla_x \log q_t - \nabla_x \log p)=0$, 
we have $q_t=p$ if we assume $p$ is a proper normalized distribution. This convergence is a well established theory, see section 1.2 and 1.3 by https://userswww.pd.infn.it/~orlandin/fisica_sis_comp/fokker_planck.pdf. There is a sufficient boundary condition where the target density $p$, e.g. a Boltzmann distribution,  decays fast at $x \to \infty$ to satisfy the normalization condition (Eq 1.85 in https://userswww.pd.infn.it/~orlandin/fisica_sis_comp/fokker_planck.pdf). In order to satisfy the boundary condtion, probability current must be uniformly zero everywhere at equilibrium. 

**1. The equilibrium of MonoFlow**:
For MonoFlow, the continuity equation is 

$$
\frac{\partial q_t}{\partial t} = \text{div} (q_t h'(\log \frac{p}{q_t})(\nabla_x \log q_t - \nabla_x \log p))
$$

if we let the probablity current (net flow) be zero, we have 

$$
q_t h'(\log \frac{p}{q_t})(\nabla_x \log q_t - \nabla_x \log p) =0
$$

Since $h' > 0$, we have $q_t (\nabla_x \log q_t - \nabla_x \log p) =0$, this is the same situation as we have for the Fokker Planck equation. 
Hence $q_t \to p$.
**We added the hypothesis in the proof, assuming the target $p$ is a Boltzmann distribution, a general assumption in energy-based models, with the potential energy $-\log p$**.

**2. The dissipation rate**:

$$
\frac{\partial\mathcal{F}(q_t)}{\partial t} = \mathbb{E}\_{q_t}{\langle \nabla\_{W_2} {\mathcal{F}(q_t)}, v_t \rangle}
$$

is a standard property of continuity equation and Wasserstein gradient flow, see Chap 11 by Ambrosio et al (2008). Replace $v_t$ by the vector filed of MonoFlow, it is trivial to see the rate wrt. KL divergence is always negative if $q_t \neq p$. Since the dissipation is the time derivative of a functional, that means this functional is always decreasing with time evolution. 

Given the above, theorem 1 tells us that MonoFlow is always decreasing KL when it converges to $p$.

**A helpful inituitive understanding of the convergence of MonoFlow**.
Since $h'$ is postive scalar function, it can be folded into the step size of Euler discritization. Different scales of $h'$ are reflected on the evolution speed of simulating the log density ratio ODE in Eq (4) which is the associated ODE of Langevin dynamics.

**Q3 : Clarity issues**

>In Section 1, authors state that "[the discriminator] is a function only depending on samples  and does not include any density information from the generator’s distribution". This statement, that is repeated throuhout the paper, is quite obscure and should be clarified. This is especially the case in Section 4.2 where it helps understanding the differences between adversarial training and VDM. Moreover, I believe that the correct statement would be that this dependency does exist, but it is ignored when optimizing the generator (as expressed by the stop gradient operator of Section 4.3).

Yes, your understanding is correct. It might depend on how we interpret the word "dependency". To avoid ambiguity, we change the words to "the discriminator cannot capture the variability of $p_g$". 

>The contribution of Section 4.1 w.r.t. results of the previous sections are not immediate and should be explicated.


>Could the authors clarify the differences between convergence requirements for $r(x , \theta)$,  $r(x , \theta\_{de})$ and $r\_{GAN}(x)$?

For $r(x , \theta)$, in order to obtain the convergence, the cost function should be $f(r)$ where $f$ is a convex function. This is derived by viewing the divergence as a function of $p_g$ or its parameter $\theta$, such that the minimum is achieved via applying Jensen-Shannon inequality - the reason why we need $f$ to be convex. This is a standard variational inference problem.

For $r(x , \theta\_{de})$ and $r\_{GAN}(x)$, the requirement is the cost function should be $-h(\log r)$ where $h$ is an increasing function. This follows our framework as amortizing MonoFlow into a parameteric generator.

>The empirical illustration of Section 5.1 does not quite match the theory without explanations.

Our sections 5.1 **does match** with the theory. We have explained that MonoFlow can work with any monotonically increasing functions. In section 5.1, 
we analyzed how the monotonicity influences the convergences of GANs by Eq (6) via the derivative $h'$ . For vanilla loss and MLE loss, the derivatives are nearly zeros when $d(x)$ (log density ratio) is less than zero. From Eq (6), we can see $\mathrm{d}x_t \approx 0$ because $h'\approx 0$. This means a slow convergence and this is the main reason causing gradient vanishing **(because the rescaled vector field is too small)**, just like an extremely small learning rate. In section 5.2, we have shown that the gradient vanishing problem can be simply fixed by just shifting the function to get non-zero $h'$.

>Less importantly, contrary to the statement in Section 3.2 that the discriminator is optimized by a "one-step gradient update".
>
This is true, we can apply multiple-step optimization to get more accurate approximation of density ratios. We have changed it to ''a few number of gradient update'' in our paper.



