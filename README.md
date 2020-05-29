# plink-182: "Assessing PCA Alternatives for Representing and Controlling for Population Stratification"

This repository will hold our code for data simulation and evaluation for
benchmarking various dimensionality reduction methods on genotyping data.

## Dimensionality reduction methods to test

| Method name | Availability | (Hyper)parameters to look into |
| --- | --- | --- |
| PCA | scikit-learn | Normalization? |
| t-SNE | scikit-learn | [Perplexity, Epsilon, etc.](https://towardsdatascience.com/how-to-tune-hyperparameters-of-tsne-7c0596a18868) |
| UMAP | umap-learn | [Metrics; also # Neighbors, Minimum Distance, etc.](https://umap-learn.readthedocs.io/en/latest/parameters.html)
| PCoA | scikit-bio | [Metrics](http://scikit-bio.org/docs/latest/generated/skbio.diversity.beta_diversity.html?highlight=beta_diversity#skbio.diversity.beta_diversity) (Jaccard, Bray-Curtis, ...) |

## Software components

Not a comprehensive list, but we'll likely have to knock out all of these modules.

Note that many of the notebooks linked here use filepaths specific to DataHub as it was set up in the Spring 2020 quarter. These notebooks will therefore need to be modified in order to work with arbitrary datasets, or on other systems.

### 1. [Data loading](https://nbviewer.jupyter.org/github/fedarko/plink-182/blob/master/notebooks/01-Load-Data.ipynb)
Load genotyping data from 1000 Genomes (or another reference dataset, but probably 1000 Genomes) into a format that's easy to work with.

### 2. [Run dimensionality reduction](https://nbviewer.jupyter.org/github/fedarko/plink-182/blob/master/notebooks/02-Run-Dimensionality-Reduction.ipynb)
Given a dataset loaded in some way, run through the full "suite" of methods + hyperparameters to test. This will produce one set of results (PCA/PCoA loadings, etc.) per method + hyperparameter set.

### 3. Evaluate dimensionality reduction results for _representing_ population stratification

#### 3.1. Compute objective score
Given the ordination space, use some sort of objective score / other magic to figure out how close together related samples are in the space -- the closer samples from similar populations are, the better. (There is likely a lot of wiggle room for ways this could be done, so it may be worth trying out different objective functions.)

Anyway, this should give us an easier "result" (just a single score or something) for each method + hyperparameter set. For now we can just output these results to a file or something -- eventually we'll want to plot them in various ways, etc.

#### 3.2. Analyze results
See how each method + hyperparameter set performed.

### 4. Evaluate dimensionality reduction results for _controlling for_ population stratification
This is going to be the more involved of the two evaluation steps, probably.

#### 4.1. Simulate phenotype data
Use GCTA / HAPGEN2 / etc.

#### 4.2. Run GWASs on the simulated phenotypes using the dimensionality reduction results as covariates
Using plink, probably (hence the project name :)

We will probably want to run multiple GWASs per method + hyperparameter set -- so that we can use different amounts of PCs and see how this impacts the GWASs. Something like the top 1, 2, 5, 10, 15 PCs (I dunno) might be good. If possible, using EIGENSOFT to figure out a "statistically significant" number of PCs and using that number here would be interesting.

Anyway, after running a GWAS, we should figure out the number of true and false hits. We should output these to a file or something, or otherwise store them somewhere.

#### 4.3. Analyze results
See how each method + hyperparameter set + PC count performed.

### 5. Test Suite
For each of these steps, we should make an effort (where feasible) to split things up into small functions that we can add tests for. This isn't very critical, but it'll be a nice thing to have. Eventually we can set up Travis for continuous integration testing, etc.
