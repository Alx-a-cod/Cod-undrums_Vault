**Conditional average and median as optimal predictors, Likelihood ratio and test, ROC curve, Naive Bayes Classifier, and Notebook on the iris dataset**.


# Conditional Average and Median as Optimal Predictors

## Problem Setup

Let:
- $( X)$: input (feature) random variable  
- $( Y )$: output (target) random variable  
- $( f(X))$: predictor estimating $( Y )$ given $( X )$  
- $( \ell(y, \hat{y}))$: loss function measuring prediction error  

We aim to choose \( f \) minimizing the **expected loss (risk)**:

$$
R(f) = \mathbb{E}[\ell(Y, f(X))].
$$

By conditioning on \( X \):

$$
R(f) = \mathbb{E}_X \Big[ \mathbb{E}_{Y|X}[\ell(Y, f(X))] \Big].
$$

Hence, for each fixed \( x \):

$$
f^*(x) = \arg\min_{a \in \mathcal{Y}} \mathbb{E}_{Y|X=x}[\ell(Y, a)].
$$

---

## Case 1 — Squared Loss ( $\ell(y,a) = (y - a)^2$ )

We compute:

$$
\mathbb{E}_{Y|X=x}[(Y - a)^2] = \mathbb{E}_{Y|X=x}[Y^2] - 2a\,\mathbb{E}_{Y|X=x}[Y] + a^2.
$$

Differentiate w.r.t. \( a \) and set equal to zero:

$$
\frac{\partial}{\partial a} \mathbb{E}[(Y - a)^2 | X=x] = -2\,\mathbb{E}[Y|X=x] + 2a = 0.
$$

Thus:

$$
\boxed{f^*(x) = \mathbb{E}[Y | X=x]}.
$$
 **Interpretation:**  
> Under **Mean Squared Error (MSE)** loss, the optimal predictor is the **conditional mean** of ( Y ) given ( X ).

---

## Case 2 — Absolute Loss $( \ell(y,a) = |y - a|)$

We minimize:

$$
\mathbb{E}_{Y|X=x}[|Y - a|].
$$

Let \( F_{Y|X=x}(y) \) be the conditional CDF of \( Y \) given \( X=x \).

Then:

$$
\frac{d}{da}\mathbb{E}[|Y - a| \mid X=x] = P(Y \le a \mid X=x) - P(Y > a \mid X=x) = 2F_{Y|X=x}(a) - 1.
$$

Setting this derivative to zero gives:

$$
F_{Y|X=x}(a) = \tfrac{1}{2}.
$$

Therefore:

$$
\boxed{f^*(x) = \text{median}(Y | X=x)}.
$$

 **Interpretation:**  
> Under **Mean Absolute Error (MAE)** loss, the optimal predictor is the **conditional median** of \( Y \) given \( X \).

---

## 4. Summary Table

| Loss Function \( \ell(y, \hat{y}) \) | Optimal Predictor \( f^*(x) \) | Statistical Meaning |
| ------------------------------------ | ------------------------------ | ------------------- |
| $(y - \hat{y})^2$ (squared loss)     | $( \mathbb{E}[Y \| X=x])$      | Conditional Mean    |
| $(y - \hat{y}$ (absolute loss)       | $( \text{median}(Y \| X=x))$   | Conditional Median  |

---

## 5. Intuition

- The **conditional mean** minimizes average *squared* deviation → sensitive to outliers.  
- The **conditional median** minimizes average *absolute* deviation → robust to outliers.  
- Both follow the **Bayes optimal prediction** rule:
  $$
  f^*(x) = \arg\min_a \mathbb{E}_{Y|X=x}[\ell(Y, a)].
  $$

---

## Cheat Sheet

| Loss                      | Optimal Predictor                  | Meaning                        |
| ------------------------- | ---------------------------------- | ------------------------------ |
| $\ell(y,a) = (y - a)^2$   | $( f^*(x) = \mathbb{E}[Y\|X=x])$   | Conditional Mean, L2 optimal   |
| $( \ell(y,a) = \|y - a\|$ | $( f^*(x) = \text{median}(Y\|X=x)$ | Conditional Median, L1 optimal |

# L²-Optimal vs L¹-Optimal Predictors

## The General Idea

When we say a predictor $f^*(x)$ is **L²-optimal** or **L¹-optimal**, we are referring to **which kind of “error norm”** it minimizes — that is, the way we measure the distance between the predicted value aaa and the true value yyy.

---
## Norm-Based Losses

Let’s define two common norms on the space of prediction errors:

- **L² norm** → measures the _root mean square_ (quadratic) deviation
- **L¹ norm** → measures the _mean absolute_ deviation
    

Formally, for a random variable $Y$ and a prediction $a$:

$$
\begin{aligned}
\text{L2 error: } &\quad \|Y - a\|_2^2 = \mathbb{E}[(Y - a)^2] \\
\text{L1 error: } &\quad \|Y - a\|_1 = \mathbb{E}[|Y - a|]
\end{aligned}
$$




We then define the optimal prediction as the minimizer of each.

---

## L²-Optimal Predictor

We minimize the **expected squared error**:

$\min_a \, \mathbb{E}[(Y - a)^2$]

The minimizer is:

$a^* = \mathbb{E}[Y]$

Therefore, **the mean is L²-optimal** — it minimizes the average squared distance between prediction and truth.

**Interpretation:**

- Errors are penalized _quadratically_ (large errors hurt much more).
- Sensitive to outliers.
- Leads to the notion of _least squares_ estimation.
    

---

## L¹-Optimal Predictor

We minimize the **expected absolute error**:

$\min_a \, \mathbb{E}[|Y - a|]$

The minimizer is:

$a^* = \text{median}(Y)$

Therefore, **the median is L¹-optimal** — it minimizes the average absolute deviation.

**Interpretation:**

- Errors are penalized _linearly_.
- More robust to outliers (a few large deviations don’t dominate the result).
- Leads to _least absolute deviations_ estimation.
  
| Concept          | L² (Mean)                              | L¹ (Median)                         |
| ---------------- | -------------------------------------- | ----------------------------------- |
| Penalizes errors | Quadratically (big errors weigh a lot) | Linearly (all errors weigh equally) |
| Outlier effect   | Pulls the mean strongly                | Median barely moves                 |
| Typical use      | Regression (MSE), Gaussian noise       | Robust estimation, Laplacian noise  |

# ~={orange}Exercise 1.1=~ — Step-by-step guide and explanation

**Prove that the conditional mean**:

$f^*(x) = \mathbb{E}[Y|X=x]$

is the **optimal predictor** under the **quadratic loss** $\ell(a,b) = (a - b)^2$

Let’s go through what’s happening **step by step**, translating the derivation into a clear logical flow.

---
## Step 1 — Problem setup

We’re in **regression** with:

- $X∈X$
- $Y∈R$

- Loss:   $\ell(a,b) = (a - b)^2$

We want to find the function $f∗(x)$ minimizing the **expected loss**:

$f^*(x) = \arg\min_{z \in \mathbb{R}} \mathbb{E}\big[ (z - Y)^2 \mid X = x \big]$

So:
> For each fixed x, we look for the scalar z that minimizes the expected squared error between z and Y, **conditional on that x**.

---

## Step 2 — Expand the expectation

We write:

$\mathbb{E}\big[ (z - Y)^2 \mid X=x \big]$

and **add and subtract** the conditional mean $\mathbb{E}[Y|X=x]$ inside the square — this is a key trick:

$(z - Y)^2 = \big[(z - \mathbb{E}[Y|X=x]) + (\mathbb{E}[Y|X=x] - Y)\big]^2$

Expanding the square:

$(z - Y)^2 = (z - \mathbb{E}[Y|X=x])^2 + (\mathbb{E}[Y|X=x] - Y)^2 + 2(z - \mathbb{E}[Y|X=x])(\mathbb{E}[Y|X=x] - Y)$

He labels these parts:

- **A** = $(z - \mathbb{E}[Y|X=x])^2$
    
- **B** = $(\mathbb{E}[Y|X=x] - Y)^2$
    
- **C** = cross term $2(z - \mathbb{E}[Y|X=x])(\mathbb{E}[Y|X=x] - Y)$
    

---

## Step 3 — Take the conditional expectation

Take expectation given $X=x$:

$$
\begin{aligned}
\mathbb{E}\big[(z - Y)^2 \mid X = x\big]
&= \underbrace{(z - \mathbb{E}[Y \mid X = x])^2}_{A} \\
&\quad + \underbrace{\mathbb{E}\big[(\mathbb{E}[Y \mid X = x] - Y)^2 \mid X = x\big]}_{B} \\
&\quad + \underbrace{2 (z - \mathbb{E}[Y \mid X = x]) \, 
\mathbb{E}\big[(\mathbb{E}[Y \mid X = x] - Y) \mid X = x\big]}_{C}
\end{aligned}
$$​​

Now, note what happens with term **C**:

$$
\begin{aligned}
\mathbb{E}\big[(\mathbb{E}[Y \mid X = x] - Y) \mid X = x\big]
&= \mathbb{E}[Y \mid X = x] - \mathbb{E}[Y \mid X = x] \\
&= 0
\end{aligned}
$$
because the mean of the deviations from the mean is zero.

So the cross term **C = 0**.

---
## Step 4 — Simplify

Thus:

$$
\mathbb{E}\big[(z - Y)^2 \mid X = x\big]
= (z - \mathbb{E}[Y \mid X = x])^2
+ \mathbb{E}\big[(Y - \mathbb{E}[Y \mid X = x])^2 \mid X = x\big]
$$

The second term (the variance) does **not** depend on \( z \).

So minimizing the left-hand side with respect to \( z \) only depends on minimizing the first term:

$$
(z - \mathbb{E}[Y \mid X = x])^2
$$

---

## Step 5 — Find the Minimum

The quadratic term $(z - \mathbb{E}[Y \mid X = x])^2$ is minimized when:

$$
z = \mathbb{E}[Y \mid X = x]
$$

Thus, the **optimal predictor** is:

$$
f^*(x) = \mathbb{E}[Y \mid X = x]
$$

---

## Step 6, Intuition (What It Means)

If you must predict $( Y )$ given $( X = x )$,  the “best” number to choose — in the sense of minimizing average squared error —  is its **conditional mean**, the expected value of $( Y )$ knowing $( X = x )$.

The remaining term (the variance) is the **irreducible error**,  which cannot be reduced by any predictor.


# ~={orange}Exercise 1.2=~ — Optimal Predictor for Absolute Loss

**Goal:**  
Find the function $f∗(x)$ minimizing the **expected absolute error**:

$f^*(x) = \arg\min_{z \in \mathbb{R}} \mathbb{E}\big[\,|z - Y| \mid X = x\big]$

---

## Step 1 — Express the conditional expectation

Let $p(y) = p_{Y|X}(y \mid x)$ be the **conditional density** of $Y$ given $X=x$. 

Define:

$\varphi(z) = \mathbb{E}\big[|z - Y| \mid X=x\big] = \int_{-\infty}^{+\infty} |z - y|\,p(y)\,dy$

Split the integral at $y=z$:

$\varphi(z) = \int_{-\infty}^{z} (z - y)p(y)\,dy + \int_{z}^{+\infty} (y - z)p(y)\,dy$

---

## Step 2 — Differentiate with respect to zzz

Differentiate under the integral sign (assuming p smooth enough):

$\frac{d}{dz}\varphi(z) = \int_{-\infty}^{z} p(y)\,dy - \int_{z}^{+\infty} p(y)\,dy$.

Recognizing that:

$F(z) = \int_{-\infty}^{z} p(y)\,dy = \mathbb{P}(Y \le z \mid X=x)$

we get:

$\varphi'(z) = F(z) - (1 - F(z)) = 2F(z) - 1$.

---

## Step 3 — Find the stationary point

To minimize $\varphi(z)$, set its derivative to zero:

$\varphi'(z) = 0 \quad \Rightarrow \quad 2F(z) - 1 = 0 \quad \Rightarrow \quad F(z) = \tfrac{1}{2}$

That means the optimal zzz satisfies:

$\mathbb{P}(Y \le z \mid X=x) = \frac{1}{2}$

---

## Step 4 — Identify the minimizer

The condition above defines the **conditional median** of $Y$ given $X=x$.

Thus, the **optimal predictor** under the **absolute loss** is:

$\boxed{f^*(x) = \text{median}(Y \mid X = x)}$

---

## Step 5 — Check convexity

Since $\varphi''(z) = 2p(z) \ge 0$,  
$\varphi(z)$ is **convex**, ensuring the median indeed minimizes the expected absolute deviation.

---

## Intuition

- Increasing $z$ makes distances to points $y<z$ larger and to $y>z$ smaller.
- The rate of change is proportional to the **difference between the probability mass** on each side:

  $\varphi'(z) = \mathbb{P}(Y \le z) - \mathbb{P}(Y > z)$
  
- The “balance point” occurs when both sides have equal probability $1/2$, i.e., at the **median**.