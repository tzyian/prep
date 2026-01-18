# Statistics


**Confusion Matrix**

|            | Predicted +ve                                                   | Predicted -ve | Metric                                                             |                                                                |
| ---------- | --------------------------------------------------------------- | ------------- | ------------------------------------------------------------------ | -------------------------------------------------------------- |
| Actual +ve | TP                                                              | FN            | $\text{Type 2 Error}=\beta=\frac{FN}{TP+FN}$<br>$=1-\text{Recall}$ | Recall: $\frac{TP}{TP+FN}$ <br>(how good it predicts +ves)     |
| Actual -ve | FP                                                              | TN            | $\text{Type 1 Error}=\alpha=\frac{FP}{FP+TN}$                      |                                                                |
| Metric     | $\text{Precision}=\frac{TP}{TP+FP}$<br>(true +ve over positive) |               | $\frac{TP+TN}{TP+TN+FP+FN}$ <br>(correct over all)                 | F1: $\frac{2\cdot Precision \cdot Recall}{Precision + Recall}$ |

**Confidence Interval**
- if we take n copies of $\bar{x}$, how many will fall within this CI

**Prediction Interval**
- wider than CI due to 

**Statistical power**
- $1 - \beta$
- the probability we correctly reject $H_0$ (correctly get a small p-value)
- gets larger when we take more samples, and  the difference between $H_0$ vs $H_1$ is larger
- (only applies when we should actually reject $H_0$)

**Standard Error (SE)**
The standard deviation of the sampling distribution of a statistic (most commonly the mean): $SE = \frac{\sigma}{\sqrt{n}}$.


p values


Heteroscedasticity

Autocorrelation



**Degrees of Freedom**
- the number of independent dimensions the variable can take i.e. the dimension of the hyperplane $\mathbb{R}^n$ the variable can fall on 
- e.g.  if it can only fall in a line, it has 1 df
- In effect, $n$ - number of degrees already taken by other variables


AUC-ROC


**R-squared, $R^2$ and Adjusted $R^2$**

AUC ROC

AIC & BIC

VIF


**MGF:**
- $E[e^{xt}]$
- generates moments
- Mean: $M(0) = E(X) = \mu$
- Variance: $Var(X) = M''(0) - [M'(0)]^2$
- Skewness (which tail):
- Kurtosis (thickness of tails)


**Distributions**
- Binomial
	- number of successes in iid trials
- Poisson
	- number of events in fixed time
- Bernoulli
	- a single attempt
- Geometric
	- number of trials until first success
- Hypergeometric Distribution
	- sampling w/o replacement
- Normal
	- 
- Uniform
	- uniform
- Exponential
	- time until next event in Poisson process
- Gamma
- Weibull

**Tests**
- t-test
- F-test
- $\chi^2$-test

Probability vs Likelihood



**Linear Regression Assumptions**
- $y=X\beta + \epsilon$, (linearity)
- $E[\epsilon\mid X] = 0$ (exogeneity, error and X uncorrelated)
- $X$ is invertible/full-rank/not multicolinear
- $Var(\epsilon \mid X) = \sigma^2 I$ (error is normally distributed)
- $Cov(\epsilon_i,\epsilon_j\mid X)=0 \text{ for } i\neq j$ (errors independent)
**L1 Regularisation (Lasso)**
- L1 norm = absolute value penalty
- $J(\beta)=\lVert y-X\beta \rVert_2^2+\lambda\lVert\beta\rVert^2_1$
**L2 Regularisation (Ridge)**
- L2 norm = squared value penalty
- $J(\beta)=\lVert y-X\beta \rVert_2^2+\lambda\lVert\beta\rVert^2_2$
- $\hat{\beta}_{ridge}=(X^TX+\lambda I)^-1X^Ty$


ANOVA

ANCOVA

**Bonferroni Correction**

**Scheffe*

**Link Function**


**Non-parametric Tests**




Maximum Likelihood Estimate

Markov Chain Monte Carlo




SSE
SSR


GLM

GAM


**CLT**



**Entropy**
- $\sum -p(x)\cdot \log(p(x))$


ARCH
ARMA, ARIMA, SARIMA,


# Machine Learning
Bias-Variance tradeoff
- bias from underfitting
- variance overfitting

SGD

SVM
Kernel Trick


SVD
PCA

tSNE

HDBSCAN

Sigmoid

Hierarchical Clustering

Bootstrapping
- sampling with replacement


Cross Entropy
ReLU


**Decision Trees**
- flow chart partitioning
- Classification
	- information gain e.g. Gini/Entropy
- Regression
	- MSE/SSE

**Random Forest**
- Classification: Take the majority value
- Regression: take the average

**Adaboost**
- forest of stumps (root with 2 leaves)
- sequentially increase weights of misclassified stumps

**Gradient Boosting**
- starts with a leaf that takes average
- sequentially
- scales all trees

**XGBoost**

**Cross Validation**
- Repeatedly spit the data by folds and running train and test

**Bagging**
- train on bootstrapped samples
**Boosting**
- sequential training on separate models



RNN
LSTM

CNN

Attention

Markov Models

RLHF

KL Divergence

Explainability (SHAP, LIME)
Data Drift ,Model decay

Discrete Markov
Continuous Markov

POMDP