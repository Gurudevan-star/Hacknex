# Technical Limitations & Forensic Boundaries

## 1. Purpose
In academic defense and real-world incident response, transparently acknowledging technical limitations is essential to scientific credibility. This document delineates the precise technical boundaries of the **Version C Cross-Vector Digital Forensics Engine**.

---

## 2. Specific Technical Limitations

### 2.1. Isolated & Encrypted Browser Profiles
- **Limitation:** In modern private browsing modes (Incognito / InPrivate), SQLite history databases are not written to disk or are held strictly in volatile memory. Additionally, browsers utilizing DPAPI or OS-level file locking can occasionally prevent reading history while the browser process is actively running.
- **System Handling:** The system creates read-only temporary clones to bypass SQLite database locks, but cannot retrieve history cleared prior to acquisition.

### 2.2. Missing Network Telemetry & TLS Encryption
- **Limitation:** The network forensics module analyzes flow metadata (destination IP, ports, DNS query logs, protocol). It does not perform TLS man-in-the-middle decryption. Consequently, HTTP request paths within encrypted HTTPS sessions cannot be inspected via network telemetry alone unless correlated with endpoint browser history.
- **Out of Scope:** JA3/TLS fingerprint reputation is **NOT IMPLEMENTED** in this version to avoid unvalidated claims.

### 2.3. Domain-Age & Live WHOIS Limitations
- **Limitation:** To ensure air-gapped forensic operation and prevent active probing (which could alert an adversary or leak victim telemetry), the engine does not perform live external WHOIS queries for domain age. Domain risk is computed strictly via lexical, structural, and TLD heuristic models.
- **Status:** Marked as **NOT IMPLEMENTED — OUT OF CURRENT SCOPE** in the Viva Claim Audit.

### 2.4. Uncalibrated Classifier Probabilities
- **Limitation:** The Random Forest prediction probability represents tree ensemble consensus, not a statistically calibrated Bayesian probability (e.g. via Platt scaling or isotonic regression).
- **Compliance:** In all user interfaces and reports, this metric is explicitly designated as **“Model probability”** rather than **“Calibrated probability”**.

### 2.5. Timestamp Uncertainty & Clock Skew
- **Limitation:** Artifacts originate from disparate sources (mail gateway server clocks, client workstation local time, DNS resolver timestamps). Clock skew between systems can introduce uncertainty in fine-grained temporal correlation.
- **System Handling:** The system relies strictly on observed timestamps. If an artifact lacks a valid timestamp header, it is assigned `"Timestamp unavailable"` rather than an interpolated estimate.

### 2.6. Legal Admissibility Boundaries
- **Limitation:** Calculating a cryptographic SHA-256 hash verifies bitstream preservation between initial acquisition and subsequent analysis. However, hashing alone does not satisfy formal legal requirements for chain of custody, evidence seizure legality, or courtroom admissibility.
- **Compliance:** All technical reports include an explicit disclaimer clarifying this distinction.

### 2.7. Cybercrime Attribution
- **Limitation:** Digital indicators (IP addresses, forged email headers, visited domains) can be manipulated via proxies, compromised relays, bulletproof hosting, and spoofed Return-Paths.
- **Ethical Rule:** The system is an analytical decision-support aid. It does not provide definitive legal attribution of criminal identity.
