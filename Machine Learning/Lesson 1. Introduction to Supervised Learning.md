---
tags:
  - ML_Decision_Theory
  - Machine_Learning
---

## Overview

This chapter introduces the **supervised learning problem**, its formalization within a **decision-theoretic probabilistic framework**, and foundational algorithmic approaches. 
It outlines <span style="color: #e5a216">key challenges</span> in <span style="color: #8392a4">generalization</span>, <span style="color: #8392a4">model evaluation</span>, and <span style="color: #8392a4">theoretical consistency</span>.

---

## From Training Data to Predictions

### Objective

>Given a set of labeled data points $(x_i, y_i) \in \mathcal{X} \times \mathcal{Y}$, the goal is to learn a function $f: \mathcal{X} \rightarrow \mathcal{Y}$ that generalizes to new inputs.

<span style="color: #e5a216">i.e.</span> Given observations (training data) of inputs/outputs pairs, $(\mathbf{x}_i, \mathbf{y}_i) \in X \times Y$ for $i=1,...,n$, the objective is to **predict the corresponding output** $\mathbf{y} \in Y$ **given a new, previously unseen input** $\mathbf{x} \in X$.

### Key Terminology

- **Inputs** <span style="color: #eb3b5a">(features/covariates)</span>: $(x) \in \mathcal{X}$
- **Outputs** <span style="color: #eb3b5a">(labels/responses)</span>: $(y) \in \mathcal{Y}$
- **Training data**: Observed labeled data used for training $(xi,yi)$
- **Testing data**: unobserved data used to evaluate performance

---

### Machine Learning vs Statistics

- **Machine Learning**: focus on algorithms, computational scalability, prediction performance.
- **Statistics**: focus on models, interpretability, theoretical guarantees.

- Both overlap, but emphasis differs!

---

### Input and Output Types

#### **Inputs $(x \in \mathcal{X})$**

- Images, audio, video, text, DNA sequences, sensor readings, etc.
- Typically embedded (representation mapped to vector spaces) in $\mathbb{R}^d$, either explicitly or via similarity/dissimilarity measures. 
- Input dimension $d$ can be very large (up to $10^6 - 10^9$ ).
  
> [!note] What does this mean?
> $\mathbb{R}^d$ ("R to the d") is a **d-dimensional real vector space**.
>
> It just means: a space of vectors (lists of numbers) with **d components**, each being a real number.
> 
>  Example: A point in 2D is $(x1,x2)∈R2(x_1, x_2) \in \mathbb{R}^2(x1​,x2​)∈R2$. 
>  A point in 3D is $(x1,x2,x3)∈R3(x_1, x_2, x_3) \in \mathbb{R}^3(x1​,x2​,x3​)∈R3$, and so on.
>  
> When we say that inputs are "embedded in Rd ${R}^d$", we mean:
    > Each **input** (like an image, a sentence, or a DNA sequence) is **converted into a vector** of real numbers of length $d$.
    > 
>    So the machine learning algorithm doesn’t work with "raw images" or "sentences" — it works with very high-dimensional **vectors of numbers** often with **millions (10⁶) or even billions (10⁹)** of dimensions, which presents both **computational and statistical challenges**.

##### Why is this a big deal? 
This leads to the <span style="color: #8392a4">curse of dimensionality</span>:
- When $d$ is large:
    - Data becomes **sparse**
    - Distance metrics and geometry become unreliable
    - Computations become **very expensive**
    - You need **a lot more data** to generalize well

So, machine learning methods must:
- Be able to **scale** to high dimensions
- Use **dimensionality reduction**, **feature selection**, or **representation learning** to manage it
#### **Outputs** $(y \in \mathcal{Y})$

- **Binary classification**: $\mathcal{Y} = \{0, 1\}\quad or \quad \{-1, 1\}$
- **Multiclass classification**: $\mathcal{Y} = \{1, \dots, k\}$
- **Regression**: $(\mathcal{Y} = \mathbb{R})$
- **Structured prediction**: Complex outputs (e.g., graphs, rankings, image segmentation)

---
### Challenges in Supervised Learning

- [ ] **Noisy labels**: $(y)$ may not be a deterministic function of $(x$);  <span style="color: #8392a4">noise</span> may stem from <span style="color: #8392a4">hidden variables</span> or <span style="color: #8392a4">labeler disagreement</span>.
``` LaTex
	Example: y = f(x) + ε y = f(x) + ε y = f(x)  + ε , where ε is random noise. <--- think med images with limited data / machine noise.
``` 

- [ ] **Complex decision functions**: $(f(x))$ may be highly nonlinear or not well-defined if $({X})$ is high-dimensional or non-vectorial.

- [ ] **Limited data**: Often requires interpolation and extrapolation, introducing risk of <span style="color: #e5a216">overfitting</span>.
             Also, the training observations ==may not be uniformly distributed in X==. 
		      **N.B.** for simplicity we assume them to be random, but some analyses will rely on deterministically located inputs to simplify some theoretical arguments.
				 ![[Pasted image 20250928150018.png]]
- [ ] **High-dimensional input space**: Known as the <span style="color: #8392a4">curse of dimensionality</span>, causing both computational (scalability) + generalization and statistical issues (generalization of unseen data).

- [ ] **Distribution mismatch**: Training and testing data may come from different distributions. That is, real-world data often differ between training and deployment.

- [ ] **Unclear performance criteria**: The metric for “good performance” (loss or accuracy) is not always well-defined or unique. Evaluation metrics may vary across applications.

---
## Formalization (Probabilistic Framework)

The traditional mathematical formalization relies on the **~={grey}decision-theoretic probabilistic framework=~**.

- **Formalization Basis:** Most modern theoretical analyses rely on a **probabilistic formulation**, viewing $(\mathbf{x}_i, \mathbf{y}_i)$ as realizations of random variables.

- **Criterion:** The goal is to maximize the expectation of some performance measure with respect to the distribution of the test data. This is often achieved by **minimizing a loss function**.

- **Core Assumption:** The random variables $(\mathbf{x}_i, \mathbf{y}_i)$ are assumed to be **independent and identically distributed (i.i.d.)** with the same distribution as the testing distribution. (Note: The book acknowledges that in practice, training data often differ from test data, but this mismatch is ignored for simplification in the main text)

### Probabilistic Framework

- Training samples $(x_i,\; y_i)$ are modeled as **i.i.d.** random variables. 

  > [!note] what's i.i.d. random variables?
  > - **Independent**: Each random variable in the set does not influence the others. The outcome of one variable doesn't affect the others.
  > - **Identically distributed**: All the random variables share the same probability distribution (same mean, variance, etc.).
  
- Goal: **Minimize expected loss (risk)** over the data distribution.
  
- **ML Algorithm Definition:** 
  A machine learning algorithm $A$ is <span style="color: #8392a4">a function that maps a dataset</span> (an element of $(X \times Y)^n$) to a function from $X$ to $Y$. 
  **The output of an ML algorithm is itself an algorithm**.
  
  Meaning:
	- **Input** : an element of $(X \times Y)^n$
	- **Output**: Output: predictor function $f:X→Y.$
	- The algorithm produces another algorithm (the predictor).
   
### Performance Evaluation in Practice

- Train and test data share the same underlying distribution.
- This book **ignores train/test mismatch**, though it is a major research topic.

---
### Probability Foundation

Probability ~={grey}P=~ is defined as a function that maps events (~={grey}Ω=~) to the range . 

The defining axioms provided are:

1. **Normalization and Impossibility:** The probability of the ~={cyan}impossible event=~ $(∅)$  is **zero**, $P(∅)=0$, 
                            and the probability of the ~={cyan}certain event=~ $(Ω)$  is **one**, $P(Ω)=1$.

2. **Additivity:** If two events A and B are mutually exclusive (disjoint, $A∩B=∅$ ), then <mark style="background: #FFF3A3A6;">the probability of their union is the sum of their individual probabilities</mark>:    $P(A∪B) = P(A)+P(B)$

### Random variables and expected value

- ~={grey} **Random Variable (X)**=~

> A **random variable (RV)**, X, is a mapping from the <mark class="hltr-yellow">sample space of outcomes (Ω) to the real numbers (R)</mark>.  $X : Ω → R$

 This transformation allows stochastic events to be analyzed using mathematical tools applicable to real numbers, such as calculating the probability that X falls within a certain interval, 
 e.g., $P(X∈[a,b])$.

Common examples of RVs, including:

 - **Bernoulli:** $X∼Ben(p)$, which models a single trial with binary outcomes (success/failure), where $P(X=1)=p$.
 - **Gaussian (Normal):** $X∼N(μ,σ^2)$.

- **~={grey}Expected Value ($E[X]$)=~**

> The **Expected Value** or expectation, $E[X]$, represents the <mark class="hltr-yellow">long-term average value of the random variable if the experiment were repeated indefinitely</mark>. 

It is the single most important summary statistic used in Decision Theory, as it forms the basis of the **Risk** function (Expected Loss).
The calculation of $E[X]$ depends on whether the variable is finite/discrete or continuous:

1. **Finite/Discrete Case:** The expectation is the <mark class="hltr-yellow">weighted sum of all possible outcomes</mark>, weighted by their respective probabilities: 
								   $E[X]=∑x⋅P(X=x)$

2. **Continuous Case:** The expectation is calculated by integration: 
                                  $E[X]=∫xf(x)dx$          (where f(x) is the probability density function)

**~={orange}Example: Rolling a Die=~** 

The die rolling example illustrates the ~={pink}discrete case=~.

- The outcomes are $X∈{1,2,3,4,5,6}$.

    - Assuming a fair die, $P(X=i)=1/6$  , for all i. 

    - The expected value is calculated as:        $E[X]=1⋅P(X=1)+2⋅P(X=2)+⋯+6⋅P(X=6)$

`-->` Result:  $E[X]= 61​(1+2+3+4+5+6)=621​=3.5$

### Conditional expectation and probability

- **~={grey}Conditional Probability=~**

> The conditional probability, $P(A∣B)$, is the probability of event A occurring _given_ that event B *has occurred*. 

This formalizes how our knowledge of one event changes our estimate of the likelihood of another.

$P(A \mid B) = \frac{P(A \cap B)}{P(B)}$

~={orange}**Example (Die Roll Continued):**=~ 

Suppose that in the example $$\begin{flalign*}& P(X = 2 \mid X \text{ is even}) = \frac{1}{3} &\end{flalign*}$$
- ~={yellow}Event B=~: X is even, $$\begin{flalign*}P(B) &= P(\{2,4,6\}) = \frac{3}{6} = \frac{1}{2} &\end{flalign*}$$
- ~={yellow}Event A=~: $$
\begin{flalign*}
& X = 2 &\\
& P(A) = \frac{1}{6} &
\end{flalign*}
$$
- $P(A∩B)$: The ~={yellow}intersection=~ is 
$$
\begin{flalign*}
& X = 2 &\\
& P(A \cap B) = \frac{1}{6} &
\end{flalign*}
$$
$$
\begin{flalign*}
& P(X = 2 \mid X \text{ is even}) = 
\frac{\frac{1}{6}}{\frac{1}{2}} = \frac{1}{3} &
\end{flalign*}
$$

Our confidence in $X = 2$ has ~={red}doubled=~ because we <mark class="hltr-yellow">restricted the sample space to only the even numbers.</mark>


### Conditional Expectation ($E[Y∣X=x]$)

> Conditional Expectation is the ~={red}expected value=~ of one random variable $(Y)$ given that we have observed a specific value for another random variable $(X=x)$.

This concept is central to predictive modeling in ML, as the goal is to find the best prediction of Y _given_ the input X.

### The Annex Example: Law of Total Probability (LTP)

The **Law of Total Probability** is a tool to compute the **overall probability of an event** when you know the probability of that event under different conditions.

Think of it like:

> “If I know what happens in each scenario, I can figure out the total chance of the event happening overall.”

##### Context and Variables

We consider a **discrete probabilistic setup** with two random variables, similar to a simplified binary classification scenario in Machine Learning:

- **Input Variable ($X$)**: Observed features, like a feature in a dataset. It can be 1, 2, or 3
    
    $X∈{1,2,3}$
    
- **Output Variable ($Y$)**: Labels or outcomes we want to predict, restricted to
    
    $Y∈{0,1}$
    
**Objective:** Calculate the **marginal probability** of observing $Y=0$, denoted $P(Y=0)$. This represents the overall likelihood of class $0$, regardless of $X$. 

Meaning: 

Find $P(Y=0)$, the **chance that Y is 0**, no matter what X is.

> The **Law of Total Probability** states that if X partitions the sample space (i.e., $X$ must be either 1, 2, or 3, but not more than one at a time), the marginal probability of $Y=0$ is the sum of the joint probabilities across all values of $X$.

The **Law of Total Probability** allows us to compute the marginal probability of Y=0Y=0Y=0 by **decomposing the probability across all possible values of X**.
### Step A: Decomposition by Joint Probability

The total probability of $Y=0$ is the sum of the **joint probabilities** of $Y=0$ occurring simultaneously with each possible value of $X$:

$$
P(Y=0) = \sum_{i \in \{1,2,3\}} P(X=i, Y=0)
$$
Expanded form:

$$
\begin{aligned}
P(Y=0) &= [P(Y=0 \mid X=1) \cdot P(X=1)] \\
&\quad + [P(Y=0 \mid X=2) \cdot P(X=2)] \\
&\quad + [P(Y=0 \mid X=3) \cdot P(X=3)]
\end{aligned}
$$

Imagine we don’t know the overall probability of Y=0. But we **can look at each X separately**:

- What is the chance Y=0 **if X=1**?
- What is the chance Y=0 **if X=2**?
- What is the chance Y=0 **if X=3**?
    

The overall probability $P(Y=0)$ is just **the sum of these “scenarios”**, weighted by how likely each scenario $(X \ value)$ is.

Formally:

$$
P(Y=0) = P(X=1, Y=0) + P(X=2, Y=0) + P(X=3, Y=0)
$$
Expanded form:

$$
P(Y=0) = P(X=1, Y=0) + P(X=2, Y=0) + P(X=3, Y=0)
$$

- Here, $P(X=1, Y=0)$ means: “What is the chance that **X=1 and Y=0** happen together?”
- These are called **joint probabilities**.


**Intuition:** We are looking at the chance of $Y=0$ in each scenario of $X$ and adding them together.

---

### Step B: Substitution Using Conditional Probability (the LTP trick)

We often don’t know the joint probabilities directly, but we can often know **conditional probabilities**, like:

$$
P(Y=0 \mid X=1) = \text{chance Y=0 if we know X=1 happened}
$$

The key formula connecting joint and conditional probabilities is:

$$
P(X=x, Y=y) = P(Y=y \mid X=x) \cdot P(X=x)
$$

Here:  
- $P(Y=y \mid X=x)$ → probability of $Y$ given $X$.  
- $P(X=x)$ → probability of that $X$ value happening.  

Substitute this into the sum from Step A:

$$
\begin{aligned}
P(Y=0) &= P(Y=0 \mid X=1) \cdot P(X=1) \\
&\quad + P(Y=0 \mid X=2) \cdot P(X=2) \\
&\quad + P(Y=0 \mid X=3) \cdot P(X=3)
\end{aligned}
$$

 `-->` This is the **Law of Total Probability**.

---
## A Real-World Analogy — The Law of Total Probability

Imagine you have three bags of balls:

- **Bag 1:** 10% red balls
- **Bag 2:** 50% red balls
- **Bag 3:** 90% red balls

We have three bags with different probabilities of containing a red ball:

| Bag | P(Bag) | P(Red) |
| --- | ------ | ------ |
| 1   | 0.2    | 0.1    |
| 2   | 0.3    | 0.5    |
| 3   | 0.5    | 0.9    |

---
You randomly pick a bag with some chance:  

Bag 1: 20%, Bag 2: 30%, Bag 3: 50%.

---

What’s the total probability of picking a **red ball**?

Using the **Law of Total Probability (LTP):**

$$
P(\text{red}) = P(\text{red} \mid B_1)P(B_1) + P(\text{red} \mid B_2)P(B_2) + P(\text{red} \mid B_3)P(B_3)
$$
---
 Substitute known values: 

$$
P(\text{red}) = (0.1)(0.2) + (0.5)(0.3) + (0.9)(0.5)
$$

---

Simplify:

$$
P(\text{red}) = 0.02 + 0.15 + 0.45 = 0.62
$$
**Result:**  
There’s a **62% chance** of picking a red ball, accounting for all bags and their probabilities.

---

## Why This Matters in Machine Learning

In **machine learning**, we often want to compute the **expected risk** (expected loss):

$$
R(f) = \sum_{x,y} P(X = x, Y = y) \, \ell(y, f(x))
$$
where:

- $( P(X = x, Y = y))$ → **joint probability** of input–output pair  
- $( \ell(y, f(x))$) → **loss** when the model predicts $( f(x)$ but the true label is $(y)$


---

Using the Law of Total Probability we can decompose the joint probability using conditional probability:

$$
P(X = x, Y = y) = P(Y = y \mid X = x) \, P(X = x)
$$

where:

- $( P(Y = y \mid X = x))$ → what the model **learns** (conditional distribution)  
- $( P(X = x))$ → what we **observe in the data** (input distribution)

Then we can calculate **total expected loss** (risk) over all inputs, and **pick the best predictor** $f(x)$ that minimizes it.

---

Substituting into the Risk Function:

Replacing this identity in the expression for \( R(f) \):

$$
R(f) = \sum_{x,y} P(Y = y \mid X = x) \, P(X = x) \, \ell(y, f(x))
$$

---

The **expected risk** represents the **total expected loss** over all possible input–output pairs.  

The goal of learning is to find the predictor \( f(x) \) that **minimizes** this risk:

$$
f^{*}(x) = \arg \min_{f} R(f)
$$
    
---

### Total Expected Loss (Risk)

So the total expected loss can be rewritten as:

$$
R(f) = \sum_{x} P(X = x) \sum_{y} P(Y = y \mid X = x) \, \ell(y, f(x))
$$
This form expresses the **Law of Total Probability over the input space**,  
showing that we average the loss over all possible inputs and outputs.

The goal is to find the predictor \( f(x) \) that **minimizes this expected risk**:

$$
f^{*}(x) = \arg\min_{f} R(f)
$$

---

so: 

The key question is:

> If we knew the true data-generating distribution perfectly, what prediction rule would achieve the best possible performance?

This removes the issue of limited training data. We want the **optimal predictor in theory**, not one estimated from samples.

To answer this, we define:

- A **loss function**
- **Expected risk**
- The **Bayes optimal predictor** (and its risk)
  
Assume data come from a fixed unknown distribution:

$p(x,y)$

on the product space $X×Y$. The marginal distribution over $X$ is $p(x)=∫p(x,y)dy$

**Expectation notation reminder**:

For a function $f:X→R$:

$$E[f(x)]=∫Xf(x) dp(x)$$

For a function $g:X×Y→R$

$$E[g(x,y)]=∫X×Yg(x,y) dp(x,y)$$
a **loss function** quantifies the discrepancy between the model's prediction and the true label. Formally, for an input–output pair $(x,y)$ and a model prediction $\hat{y}=f_w(x)$ parameterized by www, the loss is $ℓ(\hat{y}​,y)$.

$$
L(\mathbf{w}) = \mathbb{E}_{(x, y) \sim \mathcal{D}} \big[\, \ell(f_{\mathbf{w}}(x),\, y) \,\big]
$$


A supervised learning algorithm seeks parameters $w$ that minimize the **expected loss** (Generalization / Test Error)

$$
L(\mathbf{w}) = \mathbb{E}_{(x, y) \sim \mathcal{D}} \big[\, \ell(f_{\mathbf{w}}(x),\, y) \,\big]
$$

Since the data distribution $D$ is unknown, one minimizes the **empirical loss** over a dataset $\{(x_i, y_i)\}_{i=1}^{n}$ **Empirical Risk** (Training Error)

$$
\hat{L}(\mathbf{w}) = \frac{1}{n} \sum_{i=1}^{n} \ell\big(f_{\mathbf{w}}(x_i),\, y_i\big)
$$

The chosen loss function encodes what the learning problem considers “error,” and it shapes the optimization dynamics and the model's inductive bias.

### Definition: Expected Risk

Given a prediction function fff, a loss ℓ\ellℓ, and a probability distribution p(x,y)p(x,y)p(x,y) over input-output pairs, the **expected risk** is

$$R(f) = \mathbb{E}_{(x,y) \sim p} \left[\,\ell(y,f(x))\right] = \int_{\mathcal{X} \times \mathcal{Y}} \ell(y,f(x)) \, dp(x,y)$$

#### Interpretation

- Measures **true** performance across the entire data distribution.
- Also called **generalization error** or **test error**.
- We sometimes write $Rp(f)$ to emphasize dependence on $p$.
    

#### Randomness Warning

- During learning, $f$ depends on the training dataset (random).
- Therefore $R(f)$ becomes a **random variable** through $f$.
- As a functional over deterministic $f$, $R$ is deterministic.

#### Randomized Predictors

Sometimes the model outputs a **distribution over labels** for each $x$.  
In that case, the risk is also taken over this additional randomness.

---

### Definition: Empirical Risk

Given training data$\{(x_i,y_i)\}_{i=1}^n$​, the **empirical risk (training error)** is

$R_n(f) = \frac{1}{n} \sum_{i=1}^n \ell(y_i, f(x_i))$

#### Interpretation

- This is the **average loss on the training data**.
- **Random** because it depends on the random sample $\{(x_i,y_i)\}$.

# Loss Functions by Task Type

---
## 1. Regression Losses

Regression tasks predict **real-valued outputs**  
$y \in \mathbb{R}$

---

### Mean Squared Error (MSE)

$$
\ell(\hat{y}, y) = (\hat{y} - y)^2
$$

**Interpretation:**
- Penalizes large deviations **quadratically** — large errors are heavily punished.  
- Encourages **smooth predictions** (optimal predictor = conditional mean):  
  $$
  \hat{y}^*(x) = \mathbb{E}[y \mid x]
  $$
- **Convex** and **differentiable**, ideal for gradient descent.
- **Use cases:** Forecasting, regression, curve fitting, signal estimation.

---

### Mean Absolute Error (MAE)

$$
\ell(\hat{y}, y) = |\hat{y} - y|
$$

**Interpretation:**
- Penalizes deviations **linearly**, making it **robust to outliers**.  
- Optimal predictor = **conditional median**:  
  $$
  \hat{y}^*(x) = \mathrm{median}(y \mid x)
  $$
- Not differentiable at 0; gradients are constant for large errors.  
- **Use cases:** Robust regression, noisy or heavy-tailed data.

---
## 2. Binary Classification Loss

Binary tasks have labels  
$y \in \{0, 1\}$ (or equivalently $y \in \{-1, +1\}$)

---
### Binary Cross-Entropy (Logistic Loss)

Predicted probability:  $p = f_w(x) \in [0,1]$

$$
\ell(p, y) = -\left[ y \log(p) + (1 - y) \log(1 - p) \right]
$$

**Interpretation:**
- Measures divergence between **true** and **predicted probabilities**.  
- Penalizes **confident wrong predictions** heavily.  
- Equivalent to **maximum likelihood under Bernoulli noise**.  
- **Use cases:** Logistic regression, binary classifiers, neural nets.

**Alternative form** (for $y \in \{-1, +1\}$):  
$$
\ell(\hat{y}, y) = \log(1 + e^{-y \hat{y}})
$$

---
## 3. Multiclass Classification Loss

For classes $C$, labels $y \in \{1, \ldots, C\}$, and predicted probabilities  
$p \in \Delta_{C-1}$ (the simplex of class probabilities):

$$
\ell(p, y) = -\log p_y
$$
**Interpretation:**
- Generalizes binary cross-entropy to multiple classes.  
- Penalizes low probability assigned to the correct class.  
- Equivalent to **maximum likelihood under categorical distribution**.  
- Implemented as **Softmax + Cross-Entropy**.

---
## 4. Structured Prediction Loss

Structured outputs (e.g., sequences, trees, graphs, masks):  $y \in \mathcal{Y}$

Predicted sequence example:  
$$
\hat{y} = (\hat{y}^1, \dots, \hat{y}^T)
$$

### Example: Token-Level Loss
$$
\ell(\hat{y}, y) = \sum_{t=1}^T \mathbf{I}[\hat{y}^t \neq y^t]
$$

**Interpretation:**
- Captures dependencies between output components.  
- Penalizes **structural inconsistencies**, not only local errors.  
- May include advanced task-aware metrics:
  - BLEU (translation)
  - IoU (segmentation)
  - Hinge / structured SVM loss  
- Requires **structured inference algorithms** (Viterbi, beam search).

---

## Conceptual Role of Loss

The loss function is the **learning signal** guiding model optimization:

- Quantifies how *wrong* the prediction is.  
- Shapes the **sensitivity** to specific error types.  
- Defines the **optimization geometry** (convexity, smoothness).  
- Encodes **statistical assumptions** (noise model, target distribution).  
- Choosing a loss = choosing *what kind of mistakes you want to avoid*.

---

#  One-Page Cheat Sheet

| **Task Type**     | **Loss**               | **Formula**                             | **Optimal Predictor**      | **Notes / Use Case**            |
| ----------------- | ---------------------- | --------------------------------------- | -------------------------- | ------------------------------- |
| **Regression**    | **MSE**                | $(\hat{y}-y)^2$                         | $\mathbb{E}[y\mid x]$      | Smooth, sensitive to outliers   |
|                   | **MAE**                | $\hat{y}-y$                             | $\mathrm{median}(y\mid x)$ | Robust to outliers              |
| **Binary Class.** | **Cross-Entropy**      | $-[y\log p + (1-y)\log(1-p)]$           | Bernoulli MLE              | Penalizes confident errors      |
|                   | **Logistic (alt)**     | $\log(1+e^{-y\hat{y}})$                 | Margin-based               | Used in logistic regression     |
| **Multiclass**    | **Softmax + CE**       | $-\log p_y$                             | Categorical MLE            | Penalizes incorrect class probs |
| **Structured**    | **Token/BLEU/IoU/SVM** | $\sum_t \mathbf{I}[\hat{y}^t \neq y^t]$ | Task-dependent             | For sequence/graph outputs      |

---

Now that we have defined the performance criterion for supervised learning (the expected risk), the main question we tackle here is: 

> What is the best prediction function $f$ (regardless of the training data)?

The central objective in **supervised learning** is to find a prediction function  
$f$ that minimizes the **expected risk**, namely the expectation of the loss between the true label $y$ and the model prediction $f(x)$:

$$
R(f) = \mathbb{E}[\ell(y, f(x))].
$$

Using the **law of total expectation**, we can decompose the expected risk as:

$$
R(f) = \mathbb{E}_x\!\left[\mathbb{E}[\ell(y, f(x)) \mid x]\right].
$$

For a given input value $x'$, define the **conditional risk** of predicting a candidate value $z$ as:

$$
r(z \mid x') = \mathbb{E}[\ell(y, z) \mid x = x'].
$$

The overall expected risk can then be written as:

$$
R(f) = \mathbb{E}_{x'}[r(f(x') \mid x')].
$$

The **optimal predictor**, called the **Bayes predictor**, assigns to each $x'$ a value that minimizes the conditional risk:

$$
f^*(x') \in \arg\min_{z \in \mathcal{Y}} r(z \mid x').
$$

The **minimum achievable expected risk** is called the **Bayes risk**:

$$
R^* = \mathbb{E}_{x'}\!\left[\inf_{z \in \mathcal{Y}} r(z \mid x')\right].
$$

The **excess risk** of a function $f$ is how much worse it performs than the Bayes predictor:

$$
\text{Excess Risk}(f) = R(f) - R^* \ge 0.
$$

---

**What does the Bayes predictor do?**

For each input $x'$:
1. Consider every possible prediction $z$.
2. Compute the expected loss conditional on $x'$.
3. Choose the $z$ that yields the smallest conditional expected loss.

This is **pointwise optimality**:  
the decision at each $x'$ is independent of other $x$ values.

In practice, we don’t know the true conditional distribution $P(y \mid x)$,  
so learning methods attempt to **approximate the Bayes rule** from finite data.

---
 Why It’s Theoretically Optimal?

No predictor can achieve a lower expected loss than the one that minimizes conditional risk at every point $x'$.  
The Bayes predictor is therefore the **gold standard benchmark** in supervised learning.

---

## Special Cases

### 1. Binary Classification with 0–1 Loss

- Labels: $y \in \{-1, +1\}$
- Loss: $\ell(y, z) = \mathbb{I}[y \neq z]$

Then, the conditional risk when predicting $z \in \{-1, +1\}$ is:

$$
r(z \mid x') = P(y \neq z \mid x').
$$

Optimal choice:

$$
f^*(x') = \arg\max_{z \in \{-1, +1\}} P(y = z \mid x').
$$

Hence, the Bayes classifier chooses the **most probable class** given $x'$.

Define:

$$
\eta(x') = P(y = 1 \mid x').
$$

Then:

- If $\eta(x') > \tfrac{1}{2}$ → predict **+1**
- If $\eta(x') < \tfrac{1}{2}$ → predict **−1**
- If $\eta(x') = \tfrac{1}{2}$ → either prediction is optimal

The **Bayes risk** equals the **expected minimum misclassification probability**:

$$
R^* = \mathbb{E}[\min\{\eta(x), 1 - \eta(x)\}].
$$

---

### Multiclass Extension

For $k$ classes:

$$
f^*(x') = \arg\max_{i \in \{1, \dots, k\}} P(y = i \mid x').
$$

This holds for **0–1 loss**; other losses (e.g., cross-entropy) yield different Bayes rules.

---

### 2. Regression with Squared Loss

- Real-valued labels: $y \in \mathbb{R}$
- Loss: $\ell(y, z) = (y - z)^2$

Conditional risk:

$$
r(z \mid x') = \mathbb{E}[(y - z)^2 \mid x = x'].
$$

Expanding:

$$
r(z \mid x') = \mathrm{Var}(y \mid x = x') + (z - \mathbb{E}[y \mid x = x'])^2.
$$

Minimizing in $z$ gives:

$$
f^*(x') = \mathbb{E}[y \mid x = x'].
$$

Thus, the **Bayes predictor** under squared error is the **conditional mean**.

The **Bayes risk** equals the **expected conditional variance**:

$$
R^* = \mathbb{E}[\mathrm{Var}(y \mid x)].
$$

This decomposes total error into **irreducible noise** and **systematic bias**.

---

## Key Conceptual Distinctions

| **Loss** | **Bayes Rule** | **Interpretation** |
|-----------|----------------|--------------------|
| 0–1 Classification | $\arg\max_y P(y \mid x)$ | Majority vote / MAP classifier |
| Squared Loss Regression | $\mathbb{E}[y \mid x]$ | Best mean-squared estimator |
| Absolute Loss | $\mathrm{median}(y \mid x)$ | Robust to outliers |

The **loss function determines the nature of the optimal predictor.**

---

## Intuitive Summary

- **Bayes Predictor:**  
  Given full knowledge of $P(y \mid x)$, it returns the prediction minimizing expected loss.

- **Bayes Risk:**  
  The irreducible minimum loss due to inherent randomness.

- **Excess Risk:**  
  The gap between a learned model and the theoretical optimum.

> *Machine learning is the art of approximating the Bayes rule when the joint distribution $p(x, y)$ is unknown and only samples are available.*

---