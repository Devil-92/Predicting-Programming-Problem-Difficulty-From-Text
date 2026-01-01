# **Project Summary: Predicting Programming Problem Difficulty from Text**

---

## **Objective**

The goal of this project was to predict the difficulty of programming problems using **only textual information**, specifically:

1. **Problem Class** → Easy / Medium / Hard (**classification**)
2. **Problem Score** → Numerical difficulty score (**regression**)

No solution code, tags, or user feedback were used — **only problem statements**.

---

## **Step 1: Dataset Understanding & Preparation**

---

### **Dataset**

* ~4100 programming problems (**Kattis-style**)
* Available fields:

  * `title`
  * `description`
  * `input_description`
  * `output_description`
  * `problem_class` (**Easy / Medium / Hard**)
  * `problem_score` (**continuous**)

---

### **Text Construction**

All textual fields were combined into a single input:

```text
full_text = title + description + input_description + output_description
```

---

### **Cleaning**

* Lowercasing
* Whitespace normalization
* **No aggressive cleaning** (symbols and numbers preserved)

This ensured that:

* Mathematical notation
* Constraints
* Technical formatting

were retained as signals.

---

## **Step 2: Exploratory Data Analysis (EDA)**

---

### **2.1 Target Analysis**

#### **Class Distribution**

* **Easy**: 766
* **Medium**: 1405
* **Hard**: 1941

✔ Reasonably balanced — no resampling required.

---

#### **Score Distribution**

* **Range**: 1.1 – 9.7

This confirmed:

* Regression is meaningful
* Difficulty is **ordinal** (**Easy < Medium < Hard**)

---

### **2.2 Structural Text Analysis**

We analyzed how **text structure correlates with difficulty**.

Observed monotonic trend:

```text
Easy < Medium < Hard
```

Across:

* Word count
* Character count
* Digit count
* Math symbol count
* Sentence count

 **Detailed EDA plots and statistics are shown in the notebook.**

---

### **2.3 Keyword Analysis (EDA-4)**

We tested:

* Algorithm keywords (`dp`, `graph`, etc.)
* Problem-setter keywords (`constraint`, `maximum`, etc.)

**Results:**

* Keywords appeared **too rarely** (~3–4%)
* Signal was weak and sparse
* TF-IDF already captured richer semantic patterns

**Decision:**
Keyword features were **dropped** based on empirical evidence.

---

## **Step 3: Feature Engineering**

---

### **Final Feature Set**

#### **Semantic Features**

* **TF-IDF** (unigrams + bigrams, ~8000 features)

#### **Structural Numeric Features**

* Character count
* Word count
* Digit count
* Math symbol count
* Sentence count

This produced a **hybrid feature space**:

* **High-dimensional sparse** (TF-IDF)
* **Low-dimensional dense** (numeric)

---

## **Step 4: Modeling Attempts (Classification)**

---

### **4.1 Multiclass Classification**

**Models tested:**

* Logistic Regression
* Linear SVM
* Weighted numeric features
* Feature scaling
* Feature rebalancing

 **Exact accuracy values are reported in the notebook.**

**Result:**

* Accuracy consistently saturated at **~0.47–0.49**

**Confusion pattern:**

* Easy ↔ Medium confusion
* Medium ↔ Hard confusion
* **Medium** was the hardest class to separate

---

### **4.2 Regression → Class Mapping**

**Approach:**

1. Predict `problem_score`
2. Map score → class using thresholds

**Tested:**

* Random Forest Regressor
* Ridge Regression
* Numeric-only regression

**Result:**

* Regression collapsed predictions toward the mean
* Easy class was rarely predicted
* Classification accuracy decreased further

---

### **4.3 Ordinal Decomposition (Advanced Attempt)**

Classification was reformulated into **two binary decisions**:

1. Easy vs (Medium + Hard)
2. Hard vs (Easy + Medium)

Predictions were combined using ordinal logic.

**Result:**

* Slight stabilization
* Accuracy still **~0.48–0.49**

---

## **Final Diagnosis**

---

After extensive experimentation, we reached a **clear, evidence-backed conclusion**:

> **Bag-of-words representations (TF-IDF) are fundamentally insufficient for predicting programming problem difficulty.**

---

### **Why?**

* Difficulty depends on:

  * Reasoning depth
  * Algorithmic complexity
  * Constraint interpretation
* These are **semantic and logical properties**
* TF-IDF captures **lexical overlap**, not reasoning complexity

As a result:

* Easy vs Medium problems are linguistically similar
* Medium becomes an inherently ambiguous class
* Accuracy plateaus regardless of classifier choice

---

## **What Was Achieved Instead**

* Proper EDA
* Hypothesis testing
* Feature validation
* Multiple modeling strategies
* Identification of a **representation ceiling**

---

## **Final Results**

* **Best classification accuracy**: ~48–49%
* Stable across models
* Not improvable without changing representation

---

## **Future Work**

Accuracy can be significantly improved by:

1. **Semantic embeddings**

   * Sentence-BERT / MiniLM
   * Transformer-based document embeddings
2. Modeling difficulty purely as **regression**
3. Using **solution-aware or constraint-aware features**

---

## **Final Takeaway**

Despite extensive feature engineering and modeling, **difficulty classification accuracy saturated at ~48%**, indicating a **fundamental limitation of bag-of-words representations**.

This suggests that programming problem difficulty is governed more by **semantic reasoning and constraint interpretation** than surface lexical patterns.

---
