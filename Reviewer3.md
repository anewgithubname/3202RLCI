* **Why $\mathcal{T}$ must be a bijection?**

As we have explained in Lemma 3.3, the density ratio is $d(x) = \mathcal{T}^{-1}[r(x)]$, hence $\mathcal{T}$ must be a bijection such that its 
inverse exists.

* **Which precise space is $d$ optimized, and what are the hypotheses on $\phi$ and $\psi$?**

There is no restriction for the space of $d$, it can be any measurable functions, 
e.g, a linear model or a neural net. We previously followed the convenctions of standard divergence GANs where $d$'s space is not particularly specified 
but now we added this explainations in the revision. 

* **The understanding of the proof of Theorem 3.1.**

Thanks for pointing it out. The proof might not be really straightforward for people who are not familiar with continuty equation. 
First, consider the Fokker Planck equation in Eq (3),

$$
\frac{\partial q_t}{\partial t} = \text{div}(q_t(\nabla_x \log q_t - \nabla_x \log p)) 
$$

this is the probability evoluation of Langevin dynamics or the log density ratio ODE of Eq(4) where $h$ is an identity mapping. 
The equilibrium is achieved if the probabilty current (flux) $q_t(\nabla_x \log q_t - \nabla_x \log p)$ is zero, 
meaning there is no net flow and $\frac{\partial q_t}{\partial t} = 0 $. Solving the differential equation $q_t(\nabla_x \log q_t - \nabla_x \log p)=0$, 
we have $q_t=p$ if we assume $p$ is a proper normalized distribution. This convergence is a well established theory, see section 1.2 and 1.3 by https://userswww.pd.infn.it/~orlandin/fisica_sis_comp/fokker_planck.pdf. There is a sufficient boundary condition where the target density $p$, e.g. a Boltzmann distribution,  decays fast at $x \to \infty$ to satisfy the normalization condition (Eq 1.85 in https://userswww.pd.infn.it/~orlandin/fisica_sis_comp/fokker_planck.pdf). In order to satisfy the boundary condtion, probability current must be uniformly zero everywhere at equilibrium. 

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

**A helpful inituitive understanding of the convergence of MonoFlow**.
Since $h'$ is postive scalar function, it can be folded into the step size of Euler discritization. Different scales of $h'$ are reflected on the evolution speed of simulating the log density ratio ODE in Eq (4) which is the associated ODE of Langevin dynamics.
