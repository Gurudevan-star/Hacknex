# Dataset Documentation & Provenance

## 1. Compliance with the Anti-Fabrication Dataset Rule

In strict accordance with academic research standards:
- **No fictional data is disguised as live real-world enterprise telemetry.**
- **All datasets and demonstration materials are explicitly classified into two transparent tiers:**
  1. `BENCHMARK RESEARCH DATASET` (`data/benchmark_forensic_dataset.csv`): A curated, deduplicated, and statistically balanced multi-vector benchmark dataset used for academic model evaluation, feature attribution, and ablation experiments.
  2. `SYNTHETIC DEMONSTRATION DATA` (`CASE-2026-0001` in `scripts/demo_case_generator.py`): Synthetic test cases designed for safe, reproducible demonstrations without exposing proprietary or personally identifiable information (PII).

---

## 2. Benchmark Forensic Dataset Specification

- **File Location:** `data/benchmark_forensic_dataset.csv`
- **Total Records:** 200 samples
- **Target Distribution:**
  - Class 0 (Legitimate / Benign): 100 samples (50.0%)
  - Class 1 (Phishing / Malicious): 100 samples (50.0%)
- **Data Subsets:**
  - **Standard Benign Communications (75 samples):** Clean corporate communications with valid SPF, DKIM, DMARC, HTTPS, and low risk scores.
  - **Benign Edge Cases (25 samples):** Legitimate mailing list digests, customer support ticketing systems, and third-party invoice receipts exhibiting SPF softfail or Reply-To domain discrepancies.
  - **Standard Phishing Campaigns (70 samples):** Classic credential theft campaigns with urgent phrasing, failed email authentication, and raw IP or suspicious host destinations.
  - **Evasive Phishing Attacks (30 samples):** Sophisticated lures utilizing free TLS (HTTPS), compromised authorized accounts that pass SPF/DKIM, and minimal urgency phrasing, detectable primarily via browser visit history and network destinations.

---

## 3. Feature Dictionary & Extraction Schema

| Feature Name | Data Type | Value Range | Missing Value Strategy | Forensic Rationale |
| :--- | :--- | :--- | :--- | :--- |
| `https` | Integer | {0, 1} | Defaults to 0 (Unencrypted) | Phishing campaigns historically leveraged plain HTTP; modern campaigns increasingly adopt TLS. |
| `spf_result` | Integer | {0, 1} | Defaults to 0 (Failed/Unknown) | Indicates whether sending mail server IP is authorized by the domain's SPF DNS record. |
| `dkim_result` | Integer | {0, 1} | Defaults to 0 (Failed/Unknown) | Verifies cryptographic signature integrity of the message body and headers. |
| `dmarc_result`| Integer | {0, 1} | Defaults to 0 (Failed/Unknown) | Confirms domain alignment policies and authentication status. |
| `url_risk` | Integer | [0, 100] | Defaults to 0 | Lexical score assessing suspicious path components, numeric IP hosts, and excessive subdomains. |
| `domain_risk` | Integer | [0, 100] | Defaults to 0 | Heuristic score based on suspicious keywords in domain name and risky TLDs. |
| `number_of_suspicious_words` | Integer | [0, 15] | Defaults to 0 | Frequency count of urgent or credential-harvesting keywords identified in text body. |
| `reply_to_mismatch` | Integer | {0, 1} | Defaults to 0 (Aligned) | Flags when Reply-To header domain diverges from From header domain. |
| `sender_domain_mismatch` | Integer | {0, 1} | Defaults to 0 (Aligned) | Flags when Return-Path envelope domain diverges from From header domain. |
| `suspicious_network_indicator` | Integer | {0, 1} | Defaults to 0 (Normal) | Flags connections to suspicious subnets (e.g. `45.*`, `46.*`, `185.*`) or unencrypted traffic. |
| `browser_risk_score` | Float | [0.0, 100.0] | Defaults to 0.0 | Aggregated risk of visited URLs and navigation patterns extracted from local browser history. |

---

## 4. Data Leakage Prevention Methodology

1. **Deduplication:** Feature tuples are deduplicated across both classes to ensure zero sample overlap between training and testing sets.
2. **Stratified Holdout Split:** Evaluations utilize `train_test_split(..., test_size=0.3, random_state=42, stratify=y)` ensuring exact class proportions (50/50) in both training ($N=140$) and test ($N=60$) partitions.
3. **Reproducibility:** Seed `42` is fixed across data generation, partitioning, and model training.
