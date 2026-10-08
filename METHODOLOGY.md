# Scientific Methodology: AI-Based Cross-Vector Digital Forensics Engine (Version C)

## 1. Abstract & Operational Philosophy
Traditional digital forensics and incident response (DFIR) tools frequently analyze attack vectors in silos—inspecting email headers in isolation from endpoint browser history, network flows, or DNS telemetry. **Version C** implements a cross-vector digital forensics and cybercrime evidence assistance framework that systematically fuses artifacts across five primary vectors:
1. **RFC822 Email Headers & Body** (SPF, DKIM, DMARC, Return-Path alignment, lure analysis)
2. **Browser Telemetry** (Chromium / Firefox SQLite visit history, timestamps, navigation patterns)
3. **Website & URL Heuristics** (Protocol encryption, lexical structure, login form presence)
4. **Network Telemetry** (NetFlow, DNS queries, destination IP subnets, connection state)
5. **Natural Language Processing (NLP)** (Urgency scoring, social engineering lure identification)

The system operates as an **analytical decision-support assistant**. It does not make definitive claims of legal guilt or criminal attribution, but provides verifiable, evidence-backed indicators and explanations.

---

## 2. End-to-End Forensic Investigation Lifecycle

The system enforces a strict 8-stage operational pipeline:

```text
Collect  ──►  Preserve  ──►  Hash  ──►  Extract  ──►  Analyze  ──►  Correlate  ──►  Reconstruct Timeline  ──►  Explain  ──►  Report
```

### Stage 1: Evidence Collection & Intake
- Supports RFC822 `.eml` / `.msg`, MBOX archives, compressed ZIP archives, SQLite browser history (`History`, `places.sqlite`), and NetFlow / PCAP CSV exports.
- Sanitizes incoming filenames to prevent path traversal (`../`) and dangerous characters.

### Stage 2 & 3: Bitstream Preservation & SHA-256 Hashing
- Ingested files are stored in an immutable, air-gapped evidence vault (`uploads/evidence_vault/<case_id>/`).
- Computes cryptographic **SHA-256 bitstream checksums** immediately upon acquisition and records them in the case ledger (`dfir_cases.db`).
- All subsequent parsing and feature extractions execute strictly on non-destructive **working copies** (`uploads/working_copies/<case_id>/`), preserving the original bitstream unchanged.
- **Verification Engine**: On demand, the system recomputes the SHA-256 hash of the preserved file and reports `INTEGRITY VERIFIED` or `INTEGRITY CHANGED`.
- *Admissibility Note*: Hashing confirms bitstream integrity against modification; it does not by itself establish legal chain-of-custody admissibility.

### Stage 4 & 5: Feature Extraction & Forensic Normalization
- Converts disparate vector findings into a unified **Normalized Forensic Event Model**:
  - `EMAIL_RECEIVED`, `URL_EXTRACTED`, `URL_VISITED`, `DNS_QUERY`, `NETWORK_CONNECTION`, `DOMAIN_DETECTED`, `IP_DETECTED`, `CREDENTIAL_REQUEST`, `SUSPICIOUS_LANGUAGE`.
- **Absolute Anti-Fabrication Rule for Timestamps**: If an artifact lacks a reliable RFC timestamp, it is assigned `"Timestamp unavailable"`. Timestamps are never invented or interpolated.

### Stage 6: Deterministic Cross-Artifact Correlation
Correlations are established only when backed by concrete matching entities:
1. **URL Correlation**: Exact match between URL extracted from an email and visited in browser history.
2. **Domain Correlation**: Suspicious domain co-occurring across email headers, visited history, DNS lookups, or network connection destinations.
3. **DNS-Network Correlation**: DNS query host resolution directly matching the subsequent destination IP or host in TCP connection telemetry.
4. **Temporal Correlation**: Sequential proximity where user interaction (browser click or network flow) occurs within a configurable window (default: 900 seconds) after email delivery.

### Stage 7: Chronological Incident Timeline & Evidence Graph
- **Timeline**: Chronologically sorts all anchored events by UTC timestamp. Segregates unanchored events into an explicit "Timestamp Unavailable" section.
- **Topological Graph**: Generates a directed network graph: Case $\rightarrow$ Evidence Vault Files $\rightarrow$ Extracted Entities (Email, URL, Browser, Domain, DNS, IP) $\rightarrow$ Multi-Vector Correlations.

### Stage 8: Explainable AI (SHAP) & Dual Reporting
- Evaluates the 11-feature vector with an empirical tree ensemble (`RandomForestClassifier`, 100 estimators).
- Computes instance-specific Shapley values using `shap.TreeExplainer`, detailing each feature's contribution delta relative to the baseline expectation $E[f(x)]$.
- Generates two tailored reports:
  - **Technical Forensic Dossier**: Comprehensive DFIR documentation with evidence inventories, hashes, SHAP attributions, timeline, IOCs, and analyst attestation.
  - **Community Cybercrime Report**: Plain-English translation of technical findings, evidence preservation certificates, and safe protective guidance for non-experts.
