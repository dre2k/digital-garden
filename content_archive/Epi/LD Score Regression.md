---
tags:
  - StatGen
---
[[LD Score]] regression (LDSC) is a statistical method used in genetics to analyze summary-level data from genome-wide association studies (GWAS). It helps researchers distinguish between true polygenic effects and confounding factors, such as population stratification, and also allows for the estimation of heritability and genetic correlations between traits. LDSC is particularly useful because it works with publicly available summary statistics rather than requiring individual-level genetic data, making it highly efficient.

### How it Works

LDSC operates on a simple but powerful principle: a variant's association signal (measured by its chi-squared statistic, χ2) is inflated for two main reasons.

1. **[[Polygenicity]]**: The variant is in **[[Linkage Disequilibrium]] (LD)** with one or more causal variants. If a variant is in a region of the genome with many other variants (high LD), it has a higher chance of being in LD with a causal variant and will therefore have a higher χ2 statistic. The "LD score" for a variant is a measure of how much LD it has with other variants in the genome. It's calculated as the sum of the squared correlations (r2) between that variant and all other variants.
2. **Confounding Factors**: Biases like population stratification can cause inflation of the χ2 statistic across the entire genome, regardless of LD. This happens if there are subtle differences in allele frequencies between different sub -populations and those sub-populations also differ in the trait being studied.

LDSC uses these two factors to form a linear regression model:

$$E[\chi^2_j|\ell_j]​=\frac{N⋅h^2_g}{M}​​⋅\ell_j + N⋅a+1$$

- $\chi^2_j$​: The chi-squared statistic for SNP j.
- $N$: The sample size of the GWAS.
- $h^2_g$​: The SNP heritability, which is the proportion of phenotypic variance explained by all measured SNPs.
- $M$: The total number of SNPs.
- $\ell_j$​: The LD score for SNP j.
- $a$: A parameter that captures the inflation due to confounding factors.

This equation shows that the expected χ2 statistic for a variant is a function of its LD score. A plot of χ2 values against LD scores for many SNPs will show a positive linear relationship. The slope of this line is related to the heritability of the trait, while the y-intercept is related to the confounding factors.

---

### Interpreting the Results
The primary outputs of LDSC are the **slope** and the **intercept** of the regression line.

#### Heritability Estimate (Slope)
The **slope** of the regression line is directly proportional to the **SNP [[Heritability]] (hg2​)**. A steeper slope indicates a higher heritability, meaning that a larger proportion of the trait's variation can be explained by the genetic variants studied.

#### Confounding Factors (Intercept)
The **intercept** of the regression line provides insight into the presence of confounding factors.
- An intercept of **1** suggests that all the observed inflation in the χ2 statistics is due to polygenic effects (true heritability), and not due to confounding.
- An intercept **greater than 1** indicates that there's an additional source of inflation, like population stratification or cryptic relatedness, that needs to be accounted for. The closer the intercept is to 1, the less impact confounding has on the GWAS results.

This is a key advantage of LDSC over simpler methods like genomic control, which just scale down all χ2 statistics, potentially removing a true polygenic signal.

### Key Applications

LDSC is a versatile tool with several important applications in genetic research:
- **[[Heritability Estimation]]**: It provides a robust estimate of a trait's heritability from GWAS summary statistics.
- **Partitioning Heritability**: A more advanced version, called stratified LDSC (S-LDSC), allows researchers to determine if certain genomic regions or functional annotations (e.g., genes expressed in the brain, or enhancers) contribute disproportionately to a trait's heritability.
- **[[Genetic Correlation]]**: LDSC can be used to estimate the genetic correlation between two different traits. A high genetic correlation suggests that the two traits share a common genetic architecture, meaning many of the same genes influence both traits. This is useful for understanding the biological links between diseases.

---

> go more into depth regarding the intercept being an estimate of confounding factors. isn't that entirely determined by choice of the parameter 'a'> 

The model assumes that the inflation caused by confounding factors is **independent of a SNP's LD score**. This is the critical point.

**Think of it like this**: If you have two groups of people with different ancestry, and one group happens to be, on average, taller than the other, a simple GWAS would show a false association between ancestry-related genetic markers and height. This bias affects all markers in a relatively uniform way across the genome, regardless of whether a particular SNP is in a high- or low-LD region. This effect is captured by the parameter $a$

In contrast, the inflation caused by **polygenicity** (the heritability part) is directly dependent on the LD score. A SNP in a high-LD region tags many other variants, increasing its chance of tagging a true causal variant and thus inflating its chi-sq statistic

### Why 'a' is a Measure of Confounding

The reason we can interpret $a$ (and thus the intercept) as a measure of confounding is because the LDSC model mathematically separates the two sources of inflation.

1. **Inflation from polygenicity**: This is modeled by the **slope** term​, which is multiplied by the LD score ℓj​. This part of the model explicitly links the amount of signal to the local genetic architecture (LD).
2. **Inflation from confounding**: This is modeled by the **intercept** term, which is an additive constant and does not depend on the LD score. This constant represents the "baseline" inflation that would be present even for a SNP in perfect linkage equilibrium with all other variants (i.e., with an LD score of 1).

The model's core assumption is that confounding bias affects all SNPs equally, regardless of their LD. This is a reasonable assumption for broad-scale effects like population stratification. As a result, when you fit the regression line to the data, the slope captures the LD-dependent heritability signal, and the intercept captures the background, LD-independent confounding signal.

A non-zero $a$ value (and an intercept greater than 1) indicates that your GWAS results are inflated by factors other than polygenicity. The magnitude of (intercept−1) gives you an estimate of how much, on average, these factors are inflating your test statistics.

**In summary, you're correct that the intercept is determined by the parameter 'a', but the power of LDSC is precisely that it estimates this 'a' from the data, allowing it to mathematically isolate the LD-dependent (polygenic) signal from the LD-independent (confounding) signal.** This makes it a much more robust method for correcting for biases than traditional genomic control, which simply scales down all test statistics without distinguishing between the two sources of inflation.

> $a$ is estimated

Yes, exactly. The value of $a$ is not a fixed parameter you choose; it's **estimated** directly from the data by the regression model.

> so in terms of application, what precisely do you use LDSC for -- i know you can estimate heritability, and understand true signal vs confounding. what else

In addition to estimating heritability and distinguishing true polygenic signal from confounding, LD Score Regression (LDSC) has two other major applications: **estimating genetic correlation between traits** and **partitioning heritability by genomic annotations**.1 These applications have profoundly advanced our understanding of the genetic architecture of complex traits and diseases.

### Genetic Correlation

**Genetic correlation (2rg​)** measures the extent to which two different traits are influenced by the same genetic variants.3 A high genetic correlation suggests that a shared set of genes or pathways underlies both traits. LDSC can estimate this correlation using only the summary statistics from two separate GWAS.4

The method works by regressing the product of the Z-scores from the two GWAS against the LD scores of the SNPs.5 The slope of this regression line is proportional to the genetic covariance between the traits. This is a very powerful application because it allows researchers to uncover shared genetic links between seemingly unrelated traits, like the genetic correlation between educational attainment and schizophrenia, or between psychiatric disorders.

Unlike methods that require individual-level data, this approach is fast and can be applied to publicly available GWAS results, leading to a massive "atlas" of genetic correlations across human traits.6 It also has the key advantage of being robust to biases caused by sample overlap between the two GWAS.7


### Partitioning Heritability by Annotation

**Partitioning heritability** is a more advanced application, often called **stratified LDSC (S-LDSC)**.8 It allows researchers to determine if certain genomic regions or functional categories contribute disproportionately to the heritability of a trait.9

Instead of a single LD score for each SNP, S-LDSC uses multiple "LD scores" that are specific to different categories of genetic variants. For example, one LD score could be for variants located in protein-coding genes, another for variants in regulatory regions like enhancers, and so on.

The model is extended to include these different categories:

χj2​=C∑​MC​N⋅hg,C2​​ℓj,C​+…

Where 10hg,C2​ is the heritability contributed by variants in category 11C, and 12ℓj,C​ is the LD score of SNP 13j with respect to category 14C.15

By fitting this model, LDSC can estimate the heritability contribution from each category.16 This provides biological insights by answering questions like:
- Do variants in regions conserved across evolution explain a larger proportion of heritability for a given trait?
- Is the heritability for a neurological disorder enriched in genes that are primarily active in brain tissue?

This application is crucial for moving from a purely statistical understanding of genetic architecture to a more functional and biological one, helping to pinpoint the specific cell types and pathways that are most relevant to a complex trait or disease.

---

## References
- https://annahutch.github.io/PhD/LD-score-regression.html#:~:text=Original%20LDSC%20paper%3A%20LDSC%20distinguishes,across%20human%20diseases%20and%20traits
- https://www.frontiersin.org/journals/genetics/articles/10.3389/fgene.2021.753352/full
- https://annahutch.github.io/PhD/LD-score-regression.html#:~:text=Note%20that%20the%20precomputed%20LD,and%20a%20reference%20LD%20population).
- https://pmc.ncbi.nlm.nih.gov/articles/PMC6758917/#:~:text=Another%20use%20of%20LD%20Score,GWAS%20of%20a%20distinct%20trait.
- https://en.wikipedia.org/wiki/Linkage_disequilibrium_score_regression#:~:text=LDSC%20can%20also%20be%20applied,if%20used%20on%20overlapping%20samples.
- https://www.medrxiv.org/content/10.1101/2024.11.04.24316716v1.full
- https://cloufield.github.io/GWASTutorial/08_LDSC/
- https://hgen471.hakyimlab.org/post/2022/02/22/partition-heritability/#:~:text=Unlike%20the%20original%20ldsc%20(model,heritability%20given%20by%20the%20category.



