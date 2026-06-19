# IoMT Intrusion Detection System 🔐

**Network Attack Detection for Internet of Medical Things (IoMT) using Machine Learning**

[![Python](https://img.shields.io/badge/Python-3.10-blue)](https://python.org)
[![XGBoost](https://img.shields.io/badge/XGBoost-GPU-orange)](https://xgboost.readthedocs.io)
[![Isolation Forest AUC](https://img.shields.io/badge/Isolation%20Forest%20AUC-0.9456-brightgreen)]()
[![Multiclass Accuracy](https://img.shields.io/badge/XGBoost%20Multiclass-82%25-blue)]()

---

## Problem

IoMT devices (smart hospital equipment, wearables, medical sensors) are increasingly targeted by cyberattacks. A successful attack can directly endanger patients' lives. This project builds an Intrusion Detection System (IDS) trained on real network traffic to detect 18 distinct attack types — using both unsupervised anomaly detection and supervised classification.

---

## Dataset

| Property | Details |
|----------|---------|
| Domain | IoMT Network Traffic (PCAP-derived) |
| Total Records | 1,272,129 |
| Features | 38–39 network flow features |
| Attack Types | 18 + Benign |

**Attack Categories:**

| Type | Attacks |
|------|---------|
| DoS / DDoS | TCP/IP-DDoS (UDP, ICMP, SYN, TCP), MQTT-DoS (Publish Flood, Connect Flood), MQTT-DDoS |
| Reconnaissance | Recon Ping Sweep, OS Scan, Vulnerability Scan |
| Spoofing | ARP Spoofing |
| Malformed | MQTT Malformed Data |

---

## Approach

### 1. Isolation Forest — Unsupervised Anomaly Detection

Trained on benign traffic only. Detects anomalies without requiring attack labels.

| Metric | Score |
|--------|-------|
| **ROC-AUC** | **0.9456** |
| **PR-AUC** | **0.9854** |
| Threshold (95th percentile) | 0.5991 |

Per-attack detection rates:
| Attack | Detection Rate | ROC-AUC |
|--------|---------------|---------|
| MQTT_DDoS_Connect_Flood | 99.9% | 0.9998 |
| MQTT_DoS_Connect_Flood | 99.0% | 0.9965 |
| TCP_IP_DDoS_UDP1 | 89.3% | 0.9580 |
| ARP_Spoofing | 24.7% | 0.9067 |

### 2. Binary Classifiers — Per-Attack (Supervised)

One-vs-Benign classifier per attack type using Decision Tree, Random Forest, and XGBoost.

| Model | ARP_Spoofing Accuracy | Notes |
|-------|----------------------|-------|
| Decision Tree | **99.999%** | F1=0.9998 |
| Random Forest | Very high | slower |
| XGBoost | Very high | best balance |

### 3. XGBoost Multiclass — 18 Classes + Benign

Single model classifying all 18 attack types simultaneously.

| Metric | Score |
|--------|-------|
| **Accuracy** | **81.99%** |
| Macro-avg F1 | 0.7659 |
| Weighted-avg F1 | 0.8187 |
| Weighted-avg AUC | — |

**Per-class F1 highlights:**
- Benign: 1.00
- MQTT_DDoS_Connect_Flood: 0.9913
- TCP_IP_DDoS_SYN2: 0.9999
- TCP_IP_DoS_ICMP: 0.51–0.65 *(confused with similar flooding patterns)*

---

## Pipeline

```
Raw PCAP CSVs
    ↓
Data Loading & Merging (18 attack files + Benign)
    ↓
Deduplication + Column Analysis
    ↓
Missing Value Treatment + Constant Column Removal
    ↓
Feature Engineering + Mutual Information Selection
    ↓
Scaling (StandardScaler)
    ↓
┌──────────────────────────────────┐
│  1. Isolation Forest (baseline)  │
│  2. Binary Classifiers (per-att) │
│  3. XGBoost Multiclass (18+1)    │
└──────────────────────────────────┘
    ↓
Evaluation: ROC-AUC, PR-AUC, F1, Confusion Matrix
```

---

## How to Run

```bash
# Open in Google Colab (GPU recommended for XGBoost multiclass)
# iomt_intrusion_detection.ipynb

# Mount Google Drive and set dataset path
DATASET_BASE = "/content/drive/MyDrive/iomt/data/"
```

**Dependencies:**
```
pandas numpy scikit-learn xgboost joblib matplotlib seaborn
```

---

## Project Structure

```
iomt-intrusion-detection/
├── iomt_intrusion_detection.ipynb   # Full pipeline notebook
└── README.md
```

> **Dataset:** Not included due to size (~88MB). Contact author or use the CIC-IoMT-2024 dataset family.

---

## Author

**Hazem Kabbani** — [GitHub](https://github.com/hazemkabbani)

*Cybersecurity & Machine Learning Project — IoMT Network Security*
