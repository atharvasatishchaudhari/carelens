# carelens
CS441_Final_Project

# CareLens 🩺🔍  
**Robust Medical Question Classification Under Real-World Patient Language**

CareLens is an end-to-end NLP/ML project that classifies free-text, patient-style medical questions into **clinically meaningful disease categories**. The core focus is not only accuracy on curated data, but **robustness under distribution shift** (when real users describe symptoms informally) and **shortcut learning detection** (when models memorize disease keywords instead of learning semantics).

This repository contains:
- Dataset creation workflow and formatting
- Preprocessing and feature engineering (TF-IDF)
- Classical ML baselines + tuned models
- Transformer-based models (BERT, PubMedBERT, Sentence-BERT)
- Robustness experiments (including adversarial lexical scrubbing)
- Evaluation scripts, metrics, and reproducibility instructions
- Optional HuggingFace demo integration

---

## Project Motivation

In many healthcare platforms (patient portals, symptom checkers, Q&A websites), users submit medical queries in free text:
- informal language
- incomplete symptom descriptions
- no explicit disease names
- varied phrasing and ambiguity

A common failure pattern in ML prototypes is:
- Near-perfect offline accuracy on structured datasets
- Collapse to near-random performance on real user symptom language

CareLens is designed to **measure and explain this gap** through controlled experiments and robustness evaluation.

---

## What CareLens Does

Given an input medical question (or symptom description), CareLens predicts one of 10 disease categories.

Example input:
> "When I climb stairs or walk fast I start wheezing and feel tightness in my chest."

Example output:
> Asthma

---

## Key Contributions

### 1) Realistic evaluation beyond curated data
We evaluate models on an **implicit symptom dataset** where disease names are not present and language resembles real patient descriptions.

### 2) Shortcut learning / keyword memorization detection
We implement **lexical scrubbing**, where disease names are removed from the question text to test whether models rely on keywords rather than meaning.

### 3) Robustness-first experimental framework
We run a **4-experiment setup** to isolate:
- standard generalization
- reliance on disease-name shortcuts
- domain shift between curated Q&A and real symptoms
- hardest case (scrubbed training → implicit symptoms)

### 4) Broad model comparison
We compare:
- classical ML baselines (TF-IDF + linear models, trees, boosting, MLP)
- transformer classifiers (BERT, PubMedBERT)
- semantic embedding approach (Sentence-BERT + classifier)

---

## Disease Categories

CareLens supports classification into the following 10 categories:

1. High Blood Pressure  
2. Type 2 Diabetes  
3. Asthma  
4. Major Depressive Disorder  
5. Generalized Anxiety Disorder  
6. Chronic Back Pain  
7. Irritable Bowel Syndrome  
8. Osteoarthritis  
9. Chronic Kidney Disease  
10. Coronary Artery Disease  

---

## Data



### Training Dataset (Curated Q&A)
- ~300 medical Q&A pairs (≈30 per disease category)
- Curated with domain guidance (medical students + physician)
- Questions are clinically meaningful and category-specific
- Designed for a controlled, balanced training environment

Typical example format:
- `question`: "What is high blood pressure?"
- `answer`: clinically relevant explanation, symptoms, treatment, etc.
- `label`: "High Blood Pressure"

### Implicit Symptom Test Dataset
- 50 queries (5 per disease)
- Natural patient-style symptom descriptions
- **No explicit disease names**
- Created to test real-world generalization

Example:
- "Lately I feel thirsty all the time and need to use the washroom very frequently." → Type 2 Diabetes

### Lexical Scrubbing Dataset
To test shortcut learning, we generate a scrubbed version of the training dataset:
- Remove disease names from questions using regex patterns
- Preserve full answer text (answers contain rich clinical signals)
- Example:
  - Before: "What is high blood pressure?"
  - After: "What is ?"
  - Answer remains intact

This helps test whether models:
- learn semantic patterns via answers and context
- OR memorize label keywords present in questions

---

## Experiment Design

We run four experiments to separate different failure modes:

### Experiment 1: Standard (Original → Original)
- Train: 80% curated dataset
- Test: 20% curated dataset
Purpose:
- baseline performance under controlled conditions

### Experiment 2: Scrubbed Train → Original Test
- Train: 80% scrubbed curated dataset
- Test: 20% original curated dataset
Purpose:
- check if model still succeeds without disease keywords in questions

### Experiment 3: Original Train → Implicit Symptom Test
- Train: 100% curated dataset
- Test: implicit symptom dataset
Purpose:
- measure distribution shift from curated Q&A to patient language

### Experiment 4: Scrubbed Train → Implicit Symptom Test (Hardest)
- Train: 100% scrubbed curated dataset
- Test: implicit symptom dataset
Purpose:
- test true semantic robustness without keyword shortcuts

---

## Models Implemented

### Classical ML (TF-IDF Features)
- Dummy (most_frequent) baseline
- K-Nearest Neighbors
- Logistic Regression
- Linear SVC
- Random Forest
- Histogram Gradient Boosting
- MLP (feedforward neural network)
- XGBoost

### Transformer Models (Classifier Fine-Tuning)
- BERT (bert-base-uncased)
- PubMedBERT (biomedical pretraining)

### Sentence Embedding Model
- Sentence-BERT (all-MiniLM-L6-v2) + Logistic Regression / SVM head  
This approach often generalizes better to paraphrases and symptom-like language.

---

## Results Summary (High-Level)

A key finding:
- Many models achieve extremely high accuracy on curated splits
- But performance can collapse under real symptom language due to distribution shift

Another key finding:
- Sentence-level semantic embeddings (SBERT) can generalize substantially better to symptom queries than token-level classifiers in this setting.

> Full tables, plots, and per-experiment metrics are available in the notebooks.

---


