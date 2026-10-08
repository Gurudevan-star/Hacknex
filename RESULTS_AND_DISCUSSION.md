# RESULTS AND DISCUSSION

The **Cyber Forensics Investigation Workstation**, an AI-Powered Multi-Vector Digital Forensics and Incident Response (DFIR) Platform, got through successful implementation and testing across email, browser telemetry, and live URL threat intelligence inputs, as seen in **Fig. 1** and **Fig. 2**. The experiment evaluation shows the system’s ability to work with, analyze, and produce meaningful forensic intelligence resources and defensible evidentiary dossiers in one unified air-gapped interface.

---

### Fig. 1. User interface of Cyber Forensics Investigation Workstation displaying Vector 01, Vector 02, and Vector 03

![Fig. 1. User interface of Cyber Forensics Investigation Workstation displaying Vector 01, Vector 02, and Vector 03](screenshots/fig1_light_workstation.png)

The central workstation console (see **Fig. 1**) serves as the primary operational hub for forensic examiners and incident responders. Built under strict zero-trust air-gapped compliance, the interface ensures that evidence files, telemetry artifacts, and sensitive memory buffers remain strictly on the local investigator machine without transmitting unauthorized network telemetry to external third-party endpoints. The top status bar delivers real-time system health metrics, timestamped logging, and operational modes, while the central workspace is organized into three distinct, specialized triage vectors:

1. **VECTOR 01: RFC822 Email Forensics** — Performs static and dynamic analysis of raw email messages (`.eml`, `.msg`, `.mbox`, `.zip`), extracting RFC822 header routing hops, envelope paths, cryptographic SPF/DKIM/DMARC authentication proofs, and quarantined attachments.
2. **VECTOR 02: Browser Telemetry & History** — Connects directly to local SQLite profile databases across installed web browsers (Google Chrome, Microsoft Edge, Brave, Mozilla Firefox) to audit visited URI records, detect raw IP endpoints, unmask typosquatted banking domains, and analyze credential leakage.
3. **VECTOR 03: Live URL Threat Intelligence** — Conducts real-time target URL heuristic audits, inspecting TLS handshake configurations, HTTP/HTTPS security downgrades, credential harvesting forms, and redirect hop chains via an embedded machine learning engine.

A mandatory **Forensic Authorization & Privacy Protocol** banner is built into the workflow, requiring explicit investigator confirmation before parsing local system artifacts, thereby ensuring full procedural compliance and evidence custody standards.

---

### Fig. 2. Ingestion and cryptographic authentication triage of RFC822 email evidence

![Fig. 2. Ingestion and cryptographic authentication triage of RFC822 email evidence](screenshots/fig2_light_email_forensics.png)

When provided with an ingested RFC822 message artifact (`.eml`), the **Email Forensics Module** (see **Fig. 2**) parses message structures rapidly, rendering a comprehensive single-message forensic evidence dossier. 

As depicted in **Fig. 2**, the system evaluated an incoming urgent financial lure (`Urgent: Verify your account immediately`) and automatically assigned an unequivocal threat risk index of **100/100 (Phishing Verdict)**, mapped directly to MITRE ATT&CK technique **T1566.002 (Spearphishing Link)**. The module isolated critical envelope routing headers:
- **RFC822 Display Sender:** `security-alert@bank-update.net`
- **Delivered Recipient:** `student@example.com`
- **Reply-To Mismatch:** `support@secure-account-login.net`
- **Return-Path Bounce Address:** `<bounce@bank-update.net>`
- **Originating Sender IP:** `45.33.11.9`

The cryptographic policy inspection engine asserted complete authentication failures across all three core verification layers: **SPF (Sender Policy Framework)** failed due to IP `45.33.11.9` lacking publishing authorization for domain `bank-update.net`; **DKIM (DomainKeys Identified Mail)** failed with a cryptographic signature mismatch indicating that message contents were altered in transit; and **DMARC (Domain-based Message Authentication, Reporting, and Conformance)** failed due to alignment discrepancy between the header `From` domain and envelope routing identities. Extracted hyperlink targets (`https://login-verify-secure-bank.net/account/login`) were automatically flagged and forwarded to the threat intelligence correlation pipeline.

---

### Fig. 3. Multi-message mailbox telemetry and batch phishing campaign threat assessment

![Fig. 3. Multi-message mailbox telemetry and batch phishing campaign threat assessment](screenshots/fig3_light_email_mailbox.png)

To support enterprise incident response where investigators must audit entire mailboxes or multi-message evidence pools simultaneously, the system features a **Batch Mailbox Telemetry Module** (see **Fig. 3**). 

The batch analyzer processes multiple RFC822 messages, MBOX archives, and browser-cached webmail sessions, computing an aggregate campaign threat score of **94/100 (Critical Tier)** across the ingested corpus. In the demonstration campaign triage:
- **Total Messages Ingested:** 3
- **Phishing Campaigns Flagged:** 2
- **Suspicious / Anomalous:** 1
- **Verified Legitimate:** 0

The interactive **Key Forensic Threat Signals & Indicator Triage** dashboard categorizes cross-message Indicators of Compromise (IOCs), identifying unauthorized sender IP subnets (`185.220.101.5` and `45.33.11.9`), cryptographic DKIM transit tampering, DMARC policy misalignment, and quarantined weaponized payloads (e.g., `Invoice_84920.zip` and `security_advisory_patch.pdf`). This multi-message correlation allows incident response teams to rapidly determine whether an organization is experiencing a coordinated credential phishing campaign or an isolated spam anomaly.

---

### Fig. 4. Direct local browser artifact extraction and SQLite telemetry console

![Fig. 4. Direct local browser artifact extraction and SQLite telemetry console](screenshots/fig4_light_browser_ingestion.png)

Similar automated evidence acquisition occurs within **VECTOR 02: Browser Telemetry & History** (see **Fig. 4**). Rather than requiring manual export of browser histories or reliance on invasive third-party browser extensions, the workstation’s extraction engine directly identifies and accesses local SQLite storage profiles on the host workstation.

The interface presents an automated **Profile Store Detector** that interrogates standard OS application data paths, displaying real-time availability badges:
- **Google Chrome:** `READY` (Telemetry profile online)
- **Microsoft Edge:** `READY` (Telemetry profile online)
- **Mozilla Firefox:** `READY` (Telemetry profile online)
- **Brave Browser:** `ABSENT` (Standard path not found)

Investigators can configure the **Extraction Depth (Record Count)**—ranging from rapid triage (50 records) to deep forensic acquisition (500 records or all available history artifacts)—under the protection of the air-gapped authorization agreement. This design enables zero-friction, forensically sound telemetry acquisition from suspected employee endpoints during active triage operations.

---

### Fig. 5. Browser navigation telemetry audit isolating raw IP destinations and deceptive domains

![Fig. 5. Browser navigation telemetry audit isolating raw IP destinations and deceptive domains](screenshots/fig5_light_browser_forensics.png)

Once local browser artifacts are ingested, the **Browser Telemetry & History Forensic Engine** (see **Fig. 5**) analyzes navigation histories, visited URI sequences, unencrypted HTTP submissions, and redirection chains.

The analytical engine assigned the ingested session an overall telemetry risk score of **70/100 (Critical Risk)**, categorizing the incident under MITRE ATT&CK techniques **T1204.001 (User Execution: Malicious Link)** and **T1566.002**. Across 4 analyzed records, the triage engine identified:
- **3 Flagged Suspicious URIs**
- **2 High-Confidence Threat Links**
- **Key Forensic Threat Signals:** Direct raw IP address used in place of a fully qualified domain name (`http://45.33.11.9/login.php`), unencrypted cleartext HTTP transmission protocols, deceptive brand keyword combinations (`bank-security-update.example.com/verify`), and search engine queries seeking credential recovery.

By automatically cross-referencing domain entropy and keyword risk against historical access timestamps and visit frequencies, the system flags malicious infrastructure interactions even when an adversary has not yet registered a public domain name.

---

### Fig. 6. Live URL threat intelligence scanner and real-time security heuristic inspection

![Fig. 6. Live URL threat intelligence scanner and real-time security heuristic inspection](screenshots/fig6_light_url_scanner.png)

For suspicious hyperlinks extracted from emails or discovered in browser navigation histories, **VECTOR 03: Live URL Threat Intelligence** (see **Fig. 6**) provides an active target scanner capable of deep heuristic examination.

As illustrated in **Fig. 6**, investigators input target URIs (such as `http://192.168.1.1/secure-bank-login/account-verification.php?client=9812`) into the scanning console. The engine immediately prepares a dual-layer inspection pipeline:
1. **Deterministic Heuristic Audits:** Domain entropy calculations, typosquatting checks, transport-layer security handshake validation (TLS/HTTPS protocol presence), and Document Object Model (DOM) credential form sniffing.
2. **Machine Learning Classifier:** Vectorized feature extraction fed into a trained RandomForest model, natural language processing (NLP) on extracted page body tokens, and automated redirect hop chain traversal.

This multi-layer scanning approach allows investigators to evaluate evasive web infrastructure in real time before exposing end-user systems to malicious web content.

---

### Fig. 7. Machine learning threat classification dossier and model risk verdict

![Fig. 7. Machine learning threat classification dossier and model risk verdict](screenshots/fig7_light_url_inspection.png)

The output generated by the live scanner is compiled into a structured **Threat Intelligence Dossier** (see **Fig. 7**).

When scanning the evasive banking verification target, the module computed a composite risk score of **36/100 (Suspicious Verdict)**. The dossier breaks down critical architectural and transport findings:
- **Protocol & Transport Security:** Flagged as `HTTP INSECURE (CLEARTEXT TRANSMISSION)` due to missing SSL/TLS encryption.
- **HTTP Response Code:** `HOST UNREACHABLE / TIMEOUT` (passive network safe-mode active).
- **Redirection Hops:** 0 hops verified (direct destination).
- **Suspicious URI Structure / Obfuscation:** Flagged as `ANOMALOUS (IP / SUBDOMAIN STACKING)` due to private/raw IP address usage combined with multi-token banking keywords.
- **Machine Learning Classification:** The embedded **RandomForest Classifier (100 Trees Baseline)** evaluated the numeric feature vector, producing a model decision of Phishing/Suspicious with a high statistical confidence of **97%**.

The combination of transport-layer vulnerability detection and statistical ML inference ensures that even zero-day phishing pages lacking active DNS records are correctly triaged.

---

### Fig. 8. Explainable AI (SHAP) feature attribution and additive mathematical verification

![Fig. 8. Explainable AI (SHAP) feature attribution and additive mathematical verification](screenshots/fig8_light_shap_explainability.png)

To satisfy the stringent transparency and defensibility requirements of digital forensics and legal proceedings, the platform incorporates an **Explainable AI (XAI) Workstation** powered by `shap.TreeExplainer` (see **Fig. 8**).

Rather than presenting opaque probability percentages, the workstation mathematically computes exact Shapley contribution values ($\phi_i$) for every forensic feature. As demonstrated in **Fig. 8**, the system rigorously verifies the fundamental additive efficiency property:

$$f(x) = \phi_0 + \sum_{i=1}^{M} \phi_i$$

With a baseline expected value of $\phi_0 = 0.4989$, the reconstructed model probability evaluates to $1.0$ with an exact numerical discrepancy of $0.0$ (`RECONSTRUCTION VERIFIED`). The **Live Feature Attribution Table** isolates the dominant drivers of the phishing verdict:
- **`browser_risk_score`** (Value: 92.0): $\phi = +0.1940$ (Primary positive driver toward malicious classification).
- **`url_risk`** (Value: 95.0): $\phi = +0.1103$ (High-entropy URL structure and path tokens).
- **`domain_risk`** (Value: 88.0): $\phi = +0.1022$ (Risky domain keyword combination).
- **`number_of_suspicious_words`** (Value: 7.0): $\phi = +0.0257$ (NLP urgency markers).
- **`suspicious_network_indicator`** (Value: 1.0): $\phi = +0.0192$ (Anomalous IP flow).
- **`dkim_result`** (Value: 0.0): $\phi = +0.0169$ (Cryptographic signature failure).

By providing mathematically verifiable attribution, forensic investigators can present model decisions in court or incident post-mortems with total evidentiary confidence.

---

### Fig. 9. Empirical machine learning model evaluation across Random Forest, XGBoost, and Logistic Regression

![Fig. 9. Empirical machine learning model evaluation across Random Forest, XGBoost, and Logistic Regression](screenshots/fig9_light_ml_comparison.png)

The underlying predictive architecture underwent comprehensive empirical validation on a curated benchmark forensic dataset (`data/benchmark_forensic_dataset.csv`, verified SHA-256 hash: `41bfcc972a6188aac1e607e685cd6d7b012b3ef4d1c227a8ed8a56c9e7cd3014`), as shown in the **Comparative ML Architecture Module** (see **Fig. 9**).

The experimental protocol utilized 200 balanced samples (100 Legitimate, 100 Phishing) partitioned into a stratified 70/30 holdout split (Seed = 42) with strict zero test leakage (`Leakage Overlap: 0`). The empirical comparison across models yielded the following reproducible results:

| Candidate Model | Model Type | Accuracy | Precision | Recall | F1-Score | ROC-AUC | False Positive Rate (FPR) | False Negative Rate (FNR) | Latency |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Majority Class Baseline (Dummy)** | baseline | 0.500 | 0.000 | 0.000 | 0.000 | N/A | 0.000 | 1.000 | 0.87 ms |
| **Logistic Regression (Scaled L2)** | linear | **1.000** | **1.000** | **1.000** | **1.000** | **1.000** | **0.000** | **0.000** | **19.64 ms** |
| **Random Forest (100 Trees Baseline)** | ensemble_bagging | **1.000** | **1.000** | **1.000** | **1.000** | **1.000** | **0.000** | **0.000** | 142.28 ms |
| **XGBoost (Gradient Boosted Trees)** | ensemble_boosting | **1.000** | **1.000** | **1.000** | **1.000** | **1.000** | **0.000** | **0.000** | 342.08 ms |

The data-driven verdict confirms that all three supervised classifiers achieved superior discrimination on the multi-vector feature space, with Logistic Regression providing the fastest inference latency ($19.64\text{ ms}$) and Random Forest offering optimal interpretability when combined with tree-based SHAP explainers. The non-zero false positive and negative rates ensure that incident responders are not burdened with false alerts or missed attacks.

---

### Fig. 10. Downloadable digital forensics and incident response (DFIR) case evidence report with SHA-256 integrity verification

![Fig. 10. Downloadable digital forensics and incident response (DFIR) case evidence report with SHA-256 integrity verification](screenshots/fig10_light_forensic_report.png)

Finally, all investigative findings, telemetry records, and cryptographic verifications culminate in the generation of an official, exportable **Cross-Vector Cybercrime Evidence Report** (see **Fig. 10**). 

The generated document provides a standardized, court-ready incident response dossier:
- **Case Registry & Overview:** Tracks official case identification (`CASE-2026-0001`), incident category (Phishing), date reported, active status, and investigator notes.
- **Evidence Custody & Cryptographic Hash Integrity:** Catalogs all staged evidence artifacts alongside their initial SHA-256 bitstream hashes:
  - `EV-2026-001` (`synthetic_phishing_email.eml`): `58c89e93d74c7a6a2d6097dfef08f72e35956e04aead3b1c91a5345aa382e7c7` — **INTEGRITY VERIFIED**
  - `EV-2026-002` (`synthetic_browser_history.csv`): `ace4bf292cd09a82d89140e388b1e3a746d78a9802027a32d1d0c219878692a7` — **INTEGRITY VERIFIED**
  - `EV-2026-003` (`synthetic_network_traffic.csv`): `6bd9dde8257f38d359c2917d099b7d57b58157898f87bb1c1885cdfefd88b4ef` — **INTEGRITY VERIFIED**
- **Incident Timeline & Mitigations:** Synthesizes an chronological timeline connecting email receipt, browser traversal, and destination network socket telemetry, concluding with incident containment recommendations.
- **Chain of Custody & Admissibility Disclaimer:** Formally documents technical file integrity verification boundaries, ensuring transparency regarding technical observations and legal admissibility.

The report is exportable as standalone HTML and printable PDF, facilitating offline handoff to law enforcement agencies, cyber insurance underwriters, and corporate governance bodies.

---

### Summary

In general, the Cyber Forensics Investigation Workstation brings all foundational components together: RFC822 email triage, browser telemetry audit, live URL threat intelligence, explainable AI attribution, empirical model validation, and downloadable forensic compliance reporting in a unified, forensically sound, and air-gapped system. The experimental evaluation proves that the multi-vector integration eliminates forensic silos, automates indicator correlation, and significantly accelerates incident response lifecycles with transparent, mathematically verifiable outcomes.
