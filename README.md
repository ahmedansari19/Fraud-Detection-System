# Real-Time Fraud Detection System using LightGBM

## 📌 Overview
This project implements a **real-time fraudulent transaction detection system** using machine learning.  
The goal is to **detect fraud with high recall while minimizing false positives**, which is a critical requirement in real-world payment systems.

The system is built using the **IEEE-CIS Fraud Detection dataset** and follows industry-grade practices such as time-aware validation, entity-based behavioral features, threshold optimization, streaming inference simulation, and drift monitoring.

---

## 🧠 Problem Statement
Fraud detection systems must:
- Catch as much fraud as possible (**high recall**)
- Avoid blocking legitimate users (**low false positives**)
- Operate in **real time**
- Adapt to **changing behavior and data drift**

This project addresses these challenges by modeling transaction behavior over time rather than treating transactions as independent events.

---

## 📂 Dataset
- **Dataset:** IEEE-CIS Fraud Detection (Kaggle)
- **Size:** ~590,000 transactions
- **Characteristics:**
  - Highly imbalanced labels
  - Transaction-level + identity-level features
  - Realistic missing identity information
- **Note:** Raw dataset files are not included in this repository due to licensing restrictions.

---

## 🏗️ System Architecture

The system is designed as a production-style pipeline:

1. **Offline Training**
   - Feature engineering (base + behavioral + velocity)
   - Time-aware train/validation split
   - LightGBM model training
   - Threshold optimization

2. **Online Inference (Simulated)**
   - Stateful feature computation per entity
   - Real-time scoring
   - Decision policy: ALLOW / REVIEW / BLOCK

3. **Monitoring**
   - Population Stability Index (PSI) for drift detection
   - Score and feature distribution monitoring

---

## 🔧 Feature Engineering

### Base Features
- Transaction amount and time features
- Card, address, email, and device identifiers
- Processor-provided anonymized features

### Behavioral Features
- Deviation of transaction amount from historical averages
- Card-level and UID-level spending patterns

### Velocity Features
- Number of recent transactions per entity
- Time since last transaction

Entity-based aggregation was performed using:
- Card identifiers
- Composite keys (card + address)

All features were computed in a **leakage-safe, time-ordered manner**.

---

## 🤖 Modeling Approach
- **Model:** LightGBM (Gradient Boosted Decision Trees)
- **Why LightGBM?**
  - Handles missing values naturally
  - Efficient for large tabular datasets
  - Strong performance on imbalanced problems
- **Validation Strategy:** Out-of-time (chronological) split
- **Imbalance Handling:** Class-weighted loss

---

## 📊 Evaluation Metrics
Due to class imbalance, traditional accuracy is misleading.  
The following metrics were used:

- **Precision–Recall AUC (PR-AUC)**
- **Recall (Fraud Catch Rate)**
- **False Positive Count**
- **False Positive Reduction at Fixed Recall**

---

## ✅ Results

### Baseline Model
- PR-AUC: **0.5386**
- Recall: **96.0%**
- False Positives: **65,932**

### Behavioral + Velocity Model
- PR-AUC: **0.5610**
- Recall: **96.0%**
- False Positives: **59,810**

### 🔑 Key Improvement
- **False positives reduced by 9.28% while maintaining 96% fraud recall**

---

## 📈 Visualizations

### Precision–Recall Curve
Shows improved precision across most recall levels after adding behavioral and velocity features.

### False Positives vs Recall
Demonstrates a clear reduction in false positives at the same recall level compared to the baseline model.

*(Plots are included in the project report and outputs folder.)*

---

## ⚡ Real-Time Scoring Simulation
A streaming inference simulator was implemented to mimic real-world deployment:
- Transactions processed in chronological order
- Stateful entity feature storage
- Latency and throughput measurement
- Production-style decision thresholds

---

## 📉 Monitoring & Drift Detection
To ensure long-term reliability:
- **Population Stability Index (PSI)** was used to monitor:
  - Model score distributions
  - Key behavioral features
- Drift thresholds trigger alerts when significant distribution shifts are detected.

---

## 📁 Repository Structure
