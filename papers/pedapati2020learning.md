# Learning Global Transparent Models Consistent with Local Contrastive Explanations

## Topic

- explanation for black-box models
- motivation: many domains (healthcare, finance, justice) need explanations that are loyal to the deployed model

## Known

- local methods (e.g. contrastive/counterfactual explanation)
- global methods (e.g. decision trees, rule lists)

## Knowledge Gap

- global models are useful for interpretability, but may not be consistent with local explanations

## Research Question

- Is it possible to create a transparent global model that is locally consistent?
- locally consistent = consistent with the contrastive explanations

## Contribution

- natural local consistency metric: to measure local consistency
- novel method to create a globally transparent model with a higher local consistency than other known strategies

## Results

- higher local consistency than other methods
- still close in performance (accuracy)
  - remark: this method does not use the original data (labels), only the local explanations

## Data

- simple, tabular data: UCI repository

## Background

- aim: simpler but interpretable models
- can compete, but black-box models have higher accuracy - still preferable in many cases

## Concepts

- contrast points
  - perturbation of the original input, such that the number of altered features are minimal
- PP = pertinent positive
  - minimal amount of information is retained, such that it is still classified in the same class
- PN = pertinent negative
  - minimal change that would classify it to a different class
- Base Value Vector
  - least interesting values
  - user defined or median
- Upper and Lower bounds
  - general bounds for each feature
- L0 norm = number of non-zero elements in a vector
  - k-spare = at most k non-zero elements
- KDE estimate = kernel density estimation = estimate of the probability density function
- CDF function = cumulative distribution function

## Idea

### Natural consistency metric

- candidate transparent model agrees in the classification of:
  - original point
  - contrast points (PP, PN) - produced by a local explanation method

### Global transparent model

- turn contrast points into logical conjunctions of conditions (boolean clauses)
- use a custom discretization scheme to reduce features
- create features from the boolean conjunctions
- train a simple model on these features (DT, LogReg)

## Methodology

### Local consistency metric

- formal definitions of PP and PN (optimization problem)
- loss function definition
- metric = 1 - average loss over samples

### Global Boolean Feature Learning

- k-sparse PPs & PNs -> 2k-sparse Boolean AND clause -> more concise input (less dimensions)
- to be in a class, a sample:
  - has to have at least the variation of a PP
  - can have at most the variation of a PN
- regularization: binning & bounding
  - limit infinite intervals: may not be valid far from x --> reg param: delta
  - reduce the number or different clauses --> bin features to grid points
- N+1 grid points (N bins with equal probs = k/N-th quantile)
- form a binary dataset from the clauses
- fit a transparent learner to this dataset

## Experiments

- transparent learner: DT with height <= 5
- statistically significant results (paired t-tests) over 5 random split
- hyperparams tuning: 10-fold cross-validation (DT height, delta?)
- comparison
  - Standard: DT on original input - original labels
  - Distillation: DT on original input - black-box labels
  - Augmentation: DT on original input + contrast point - black-box labels
- quant eval
  - higher local consistency
  - high performance on PNs
  - accuracy is still competitive
  - features are only built on sparse local explanation, w/o access to original data
- qual eval
  - resultant model is still transparent
  - 

## Open questions

- why not using the original labels? --> aim: post-hoc global surrogate model, to avoid bias?
- why to train a transparent model on the boolean features? Why not just using the boolean forms as a conjunction?
- One feature == conjunction of all conditions for one sample?
- Max number of features == |D| ?
- What are the top features of a DT?

## Erros?

- What is calligraphic k in section 3?
- Brackets in PP def (section 3)?
- Alg 1, line 14 n(xj)