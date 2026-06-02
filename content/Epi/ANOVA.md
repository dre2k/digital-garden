---
tags:
  - StatGen
  - Biostats
draft: true
---
Typical use case is comparing two means differences for statistical significance

In the context of variance, if between group variance is different than within group variance, indication that the distributions of the variables are different
- between group is weighted sum of squares between group variance and overall mean variance
- within group is weighted sum of squares within each group (in other words, weighted average of the k individual group variances)
- f-test = between group / within group. null F-stat = 1 (same values for both)
- statistical test is based on F-test

![[Screen Shot 2022-07-27 at 11.42.34 AM.png]]


ANOVA as a linear equation (factor effects model)
$$Y_{ij} = \mu + \tau_i + \epsilon_{ij}$$
in applied example (say, waiting time for treatment, and grouping is hospitals):
- $Y_{ij}$ is the individuals outcome
- $\mu$ is average outcome over all individuals
- $\tau_i$ is average outcome over all individuals in a particular hospital

![[Screen Shot 2022-10-01 at 9.47.23 AM.png]]

the $\tau$ term is added or subtracted from the overall mean $\mu$ depending on the hospital. 

assumption -- sum of $\tau$ = 0 (let's try to understand this)
- this is a 'constraint'
- firstly - remember tau is categorical -- it's not modeled as a continuous variable
- So take this model  $Y_{i,j} = μ_i + \epsilon_{i,j}$ (cell means model)
	- think of this as predicting an individuals' response simply by reporting their groups average response
- now take this model $Y_{i,j} = μ + τ_i +  \epsilon_{i,j}$ (factor effects model)
	- note that the overall mean is like an intercept, and you 'tweak' it by using the specific's group contribution
	- unlike the img above, $\tau$ is actually the difference between the overall mean and the mean for the level $i$ 
- to tie these two :    $μ_i = μ + τ_i$
- suppose you sum those two like this --- $∑ μ_i = rμ + ∑ τ_i$ (for r number of groups)
	- then $rμ = rμ + ∑ τ_i$
		- which means we should constrain the sum of tau's to equal ZERO
		- without this constraint, you get infinite possible answers, and you can't estimate the parameter
		- (note -- this isn't the only type of constraint you can put on this. I wonder how constraints are codified in stats packages --- **looks like stats packages do it e.g. SAS**)
		- also -- i think this is a simplified case when all the groups have the same size, complicates things when they don't

importantly - in the linear form we're testing
$$H_0: \tau_i = 0$$
**for all i** (.. e.g. hospital). so if the hospital effects are all zero, then the average effect across all groups is the same, and we don't reject the hypothesis



## two-way ANOVA
you include a term for the second grouping variable, and an interaction term (not sure if this is the case only when it's nested groups e.g. departments within each hospital)


$$Y_{ijk} = \mu + \tau_i + \gamma_j + \beta_{ij} + \epsilon_{ijk}$$

---

## References

https://towardsdatascience.com/anova-for-feature-selection-in-machine-learning-d9305e228476

(I'm beginning to think some of the terms she used here are not appropriate but it was a good crumb trail for concepts)
https://www.rebeccabarter.com/blog/2017-02-17-anova/


![[Rebecca Barter - ANOVA.pdf]]

![[Statistics 512 Applied Linear Models - ANOVA.pdf]]
## questions
- anova third assumption that group variances need be identical - what's up with that, and isn't that what we're testing with anova in the first place
	- i think to in order to compare group means, you the distribution between data source should be consistent? 


FYI
- the F-test components ARE weighted (by the sample sizes of each group. IF they're the same size that's a special case where you don't need to weight)




--- 
## controlling for multiple testing
- least significant difference
- bonferroni
- tukey
- scheffe
- 