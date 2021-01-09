---
title: "Which is a good summary of a multimodal posterior?"
author: Lorenzo Schiavon
date: "2021-01-11"
tags: ["Mixture Models", "Cluster Allocations", "Bayesian statistics", "MCMC", "Factor models", "Identifiability"]
toc: true
toc_sticky: true
---


## Multimodal posterior distributions

In this discussion we deal with two cases that generates multimodal distributions

 - Large discrete parameter spaces: not ordered. (Ex: partition spaces in clustering).
 - Non-identifiability/symmetry in the parameter space: label switching, sign switching ... (Ex: mixture parameters, loadings matrix in factor analysis...)

Problem in well done MCMC: we obtain $$S$$ ($$t=1,...,S$$) samples that are drawn from the more than one modes of the posterior distribution.


**Why usual summaries does not work?**

 - MCMC mean: not meaningful! Let's think to the mean between two modes... 
 - MCMC mode: not feasible! The parameter space is infinite (or very very large) so that the points are visited only once by the chain.
 - MCMC median: ordering?

Let's look in details the most common cases where summaries of multimodal posterior are desired.

## Bayesian clustering
#### Notation: 
 - $$S$$ samples, 
 - $$k$$ possible groups, 
 - observation $$n$$ belongs to group $$g_n$$.	
  
Let's start with the simpler case!
  
Why simpler?
 - We can reduce the parameter space to the partition space: absence of symmetry (usually...).
 - Extensive literature: Dahl (2006), Wade and Ghahramani (2018) ... Stival (202*) ;) 

**Problem**: large and not ordered discrete space. 

**Solution**: identifying a measure of distance between partitions A and B. For instance,

$$
D(A,B) = \sum_{n <n'} \{1(g^A_n \neq g^A_{n'})1(g^B_n = g^B_{n'})  + 1(g^A_n = g^A_{n'})1(g^B_n \neq g^B_{n'}) \}.
$$

Then, a representative summary of the posterior is the partition $C$ that minimizes this distance (as well the mean is for L2, the median for L1,...). 
$$C$$ is approximated by

$$
\hat{C} = \text{argmin}_c \sum_{t=1}^S D(c,t) = \sum_{n <n'} \{1(g^c_n = g^c_{n'}) - p_{n,n'}\}.
$$

where $$p_{n,n'} = \sum_{t=1}^S 1(g^t_n = g^t_{n'}) /S$$ is the posterior similarity matrix. It's a decision theory approach!

This summary was that considered by Dahl (2006). 
Wade and Ghahramani (2018) show that the problem can be reduced to choose a suitable loss function and an associated distance metrics, proposing better alternative summaries. 
	
## Mixture parameters
Less difficult? We have continuous parameter space where we can use usual distance metrics and summaries (e.g. mean).

More difficult? there is symmetry between different points of the parameter space.

Different problem: we just need a synthetic summary for interpretation purposes which takes into account the symmetry. 
Mean: not meaningful, mode: not feasible (continuous parameter space).
		
**Solution**: re-labels the parameters trying to minimize a Loss function (again decision theory!).
Several possibilities for the loss function: 
 - distance from a specific point (but which point? that's the question);
 - the loss function of the related clustering process
 - ...

...or just use as representative the parameters of the iteration with the best clustering (we will return later).


## Loadings matrix in factor analysis
In mixture symmetry was just due to group label switching. 
	
In FA we have infinite possible rotations of $\Lambda$.

$$
\Omega = \Lambda \Lambda^\top +\Sigma = \Lambda_P \Lambda_P^\top +\Sigma
$$

where $$\Lambda_P = \Lambda P$$ with $$P$$ any rotation matrix such that $$P^\topP=I$$.
	
**Solution**: usual approaches follows again decision theory.
Varimax to orthogonalize each sample (loss function is invariant with respect to column label switching and sign of the columns).

 **+** algorithms used in mixture problem to align the column labels.
 **+** algorithms that minimize loss function to align the sign of each column.


## My specific problem with loadings matrices
In my factor model I need to interpret the loadings matrix (so I need a synthetic and informative summary).
But I don't want to rotate/post-process the samples of Lambdas because I add, through informative priors, information about specific elements $$(i,j)$$ of $$\Lambda$$ and I want to infer the role of this information.

So, summaries obtained from rotated samples are not interesting for me.

**My proposed solution** : NO postprocessing.

Choose a representative $$\Lambda_{(t^*)}$$ of the MCMC in a smart way.

For instance, $$\Lambda_{(t^*)}$$ which maximizes the non-normalized posterior.
Approximate the mode maximizing the non-normalized posterior is a good approach also for mixture and clustering.

But this is possible *only* when the marginal likelihood, obtained integrating out the other parameters, is available in a closed form...

*...In practice, a closed form is often unavailable...*

## So, it's your turn!
I suppose that alternative Loss function could be worth explored, but which?
Any other ideas?

## Bibliografia

  -  Dahl, D. B. (2006) Model-based clustering for expression data via a Dirichlet process mixture model. In \textit{Bayesian Inference for Gene Expression and Proteomics }(ed. M. V. Kim-Anh Do, Peter Muller), vol. 4, 201--218. Cambridge University Press.	
  -  Kaiser, H. F. (1958). The varimax criterion for analytic rotation in factor analysis. \textit{Psychometrika}, 23(3), 187-200.
  - Stephens, M. (2000). Dealing with label switching in mixture models. \textit{Journal of the Royal Statistical Society: Series B (Statistical Methodology)}, 62(4), 795-809.
  - Wade, S., and Ghahramani, Z. (2018). Bayesian Cluster Analysis: Point estimation and credible balls (with Discussion).\textit{ Bayesian Analysis}, 13(2), 559–626. https://doi.org/10.1214/17-BA1073	
