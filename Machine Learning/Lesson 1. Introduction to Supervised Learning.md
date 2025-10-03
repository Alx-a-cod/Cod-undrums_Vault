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
> When we say that inputs are "embedded in Rd\mathbb{R}^dRd", we mean:
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
- [ ] **Unclear performance criteria**: Evaluation metrics may vary across applications.

---
## Formalization (Probabilistic Framework)

The traditional mathematical formalization relies on the **decision-theoretic probabilistic framework**.

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

## 2.3 Learning Algorithms

### 🔧 Two Main Classes

1. **Local Averaging Methods**
2. **Empirical Risk Minimization (ERM)**

These will be explored in more detail in later chapters.

---

## 2.4 Statistical Consistency

- Evaluates whether a learning method converges to the optimal predictor as sample size increases.
- Consistency is the central theoretical focus of the book.

---

## 2.5 No-Free-Lunch Theorems

- ⚠️ No learning algorithm can perform well across **all** distributions.
- Meaningful guarantees require assumptions about the data or target function.

---

## 2.6 Making Assumptions

- Necessary to obtain **quantitative results** and **generalization bounds**.
- Examples:
  - Smoothness of \(f\)
  - Margin conditions
  - Noise models

---

## 2.7 Extensions to the Basic Framework

- Structured prediction
- Semi-supervised learning
- Online learning
- Domain adaptation

---

## 2.8 Summary and Book Outline

- Main goal: Theoretical understanding of supervised learning
- Focus on:
  - Learning guarantees
  - Algorithmic frameworks
  - Consistency and generalization
- Chapters build on this foundation:
  - 📘 Chapter 3: Least Squares & Linear Models
  - 📘 Chapter 4: Model Selection & Generalization Bounds
  - 📘 Chapter 7 & 9: Learning Representations

---

## 🧪 Practical Considerations

### ✅ Model Evaluation

- **Data Splitting**:
  - Training set: Model fitting
  - Validation set: Hyperparameter tuning
  - Test set: Final evaluation
- **Cross-Validation**:
  - Often preferred (e.g., k-fold with \(k=5\) or \(10\))
  - Reduces overfitting and estimation variance

> ⚠️ Note: Reusing test data multiple times inflates performance estimates.

### 🛠️ Model Selection

- Based on:
  - Validation performance
  - Penalization criteria (e.g., regularization)
- Discussed further in Chapter 4.6

---

## 📊 Design Paradigms

- **Random Design**: \((x_i, y_i)\) are i.i.d. samples (default)
- **Fixed Design**: Inputs \(x_i\) are fixed/deterministic
  - Common in theoretical analysis
  - Error evaluated on same \(x_i\), but different \(y_i\)

---

## 📌 Summary

- Supervised learning seeks to predict labels from inputs using labeled training data.
- It is formalized via probabilistic models with a focus on minimizing expected loss.
- Theoretical understanding requires analyzing learning algorithms for **consistency**, **generalization**, and **scalability**.
- Practical aspects include data partitioning, model selection, and validation techniques.
