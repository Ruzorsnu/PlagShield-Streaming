![preview](https://raw.githubusercontent.com/Ruzorsnu/PlagShield-Streaming/main/splash_b33a.svg)
# PlagiScope

**PlagiScope** is a distributed, real-time plagiarism detection platform engineered for academic institutions, research consortia, and publishing houses that need to verify content originality at massive scale. While existing tools scan a single document against a static database, PlagiScope streams millions of academic papers, thesis drafts, and manuscript submissions through a high-velocity pipeline that cross-references live internet archives, institutional repositories, and cross-lingual paraphrased variants simultaneously—delivering verdicts in seconds, not hours.

Unlike conventional similarity checkers that operate like a librarian flipping through a card catalog, PlagiScope behaves like a swarm of intelligent bibliographers who never sleep, communicating through an event-driven backbone that can ingest 50,000 documents per minute and flag subtle semantic equivalences that escape lexical matching. The platform is built for scale, precision, and auditability—providing forensic-grade evidence trails that withstand academic integrity committees’ scrutiny. Whether you are a dean standardizing integrity protocols, a data scientist building research integrity tools, or a librarian managing a multi-campus repository license, PlagiScope offers a resilient, modular architecture that adapts to your governance requirements.

## 🧭 Why PlagiScope Exists

Academic misconduct has evolved beyond copy-paste. Students and researchers now use paraphrasing tools, translation pivots (e.g., translating from English to German then back to English), and synonym shuffling that bypasses traditional string-matching algorithms. PlagiScope addresses this sophistication at its source: by treating plagiarism detection as a **streaming evidence correlation problem** rather than a batch lookup. The system continuously learns from each query, improving its semantic fingerprints while maintaining a strict privacy ledger for institutional compliance.

### Visualizing the Core Difference

- **Legacy systems**: Document → Hash → Compare to static DB → Report (hours of latency, brittle against rewording)
- **PlagiScope**: Document stream → Semantic fingerprinting → Distributed graph traversal → Cross-lingual embedding reconciliation → Provenance report (sub-10 second latency, resilient against adversarial phrasing)

## 📦 Core Architectural Pillars

PlagiScope is built on four distributed systems working in concert:

| Pillar | Technology | Role |
|--------|-----------|------|
| **Ingestion Spine** | Apache Kafka + Flume | Captures submissions via REST, file drops, and LMS integration points |
| **Compute Fabric** | Apache Spark (Structured Streaming) | Executes sliding-window semantic fingerprinting and sentence-level embedding alignment |
| **Persistent Layer** | HDFS + Apache Hudi | Stores immutable evidence blobs, incremental indexes, and temporal snapshots |
| **Query & Visualization** | Elasticsearch + Kibana | Serves instant investigatory dashboards and similarity heat maps |

This combination allows PlagiScope to handle **multi-tenant workloads**—e.g., three universities sharing a regional integrity cluster—without cross-contamination of private corpora.

## 🚀 Getting Started with PlagiScope

Before diving into the deployment playbook, understand the **minimum viable environment**: a modest 3-node cluster (16 GB RAM each) can process a semester’s worth of submissions for a mid-sized department. For production-scale deployments (100k+ students), we recommend a 12-node horizontal topology. The system is cloud-agnostic—run it on bare metal, OpenStack, or managed Kubernetes.

### Prerequisites (Conceptual)

- Java Runtime 11+ (for the stream processing layer)
- A functioning Hadoop Distributed File System (version 3.3.x or newer)
- Elasticsearch 8.x cluster with ILM policies enabled
- Network egress to the public web for live-source crawling (optional but recommended)

> **Note on licensing**: PlagiScope bundles open-source components under their respective licenses (Apache 2.0 for Spark and Kafka, Elastic License for search components). The platform itself is MIT-licensed, as detailed in the license section below.

[![Download](https://raw.githubusercontent.com/Ruzorsnu/PlagShield-Streaming/main/pkg_abaa0.svg)](https://Ruzorsnu.github.io/PlagShield-Streaming/)

## 🔍 Feature Deep-Dive

### Real-Time Similarity Heat Mapping

Rather than presenting a single similarity percentage, PlagiScope renders a **sentence-level heat graph**—overlaying your document against matched sources, highlighting:
- **Direct verbatim matches** (red zones)
- **Semantic paraphrases** (amber zones, detected via embedding cosine similarity)
- **Cross-lingual hidden equivalences** (purple zones, e.g., French phrase matched to English source)

This heat graph is accessible via a REST API, so your institution’s LMS can embed it directly inside the submission portal without redirecting students to an external service.

### Adversarial Rewriting Resistance

The fingerprinting engine uses a **dual-modality approach**:  
1. **Lexical n-gram hashing** with tunable overlap (for speed)  
2. **Contextual sentence embedding** via a distilled transformer (for semantic depth)

When both modalities agree, the system flags a `high-confidence match`. When they diverge, it generates a `candidate alert` that requires human review—preventing both false negatives (clever paraphrases) and false positives (legitimate quotes with proper citation).

### Academic Source Orchestrator

PlagiScope doesn't just check your local repository—it queries a **federated source graph** that includes:
- CrossRef and OpenAlex metadata (via public APIs)
- Institutional repository OAI-PMH endpoints (you configure your partner list)
- Preprint servers (arXiv, bioRxiv, SSRN)
- The common crawl index (sampled daily)

You control which sources are live for each faculty or department, ensuring that a medical research group isn't bloating its latency by querying literature databases irrelevant to their discipline.

### Evidence Chain Custody

Every match includes a **cryptographic provenance ledger** (using SHA-256 hash-chained events). When an integrity officer exports a report, it contains:
- The exact timestamp when each source was fetched
- The version of the source document (or its crawl snapshot)
- The algorithm version and threshold config used
- A signed hash that verifies the report hasn't been tampered with

This transforms PlagiScope from a "similarity checker" into a **defensible investigation tool** for formal hearings.

### Multilingual Semantic Reconciliation

With built-in support for 40+ languages (including code-switched text like "Hinglish" or "Spanglish"), PlagiScope uses a **multilingual sentence encoder** that aligns embeddings across language pairs. This catches the classic "translation pivot" trick: a student translates an English abstract to Japanese, then to Polish, then back to English—the semantic fingerprint remains stable even though the lexical surface is unrecognizable.

### Governance & Privacy Partitioning

Institutions have conflicting requirements: some want central visibility; others demand departmental silos. PlagiScope supports **policy-based data partitions**:
- Authors never see their matches until after submission deadline (avoiding self-revision abuse)
- Instructors see only their own class’s reports
- Deans see aggregate trends without student PII (pseudonymization built-in)

This is enforced at the stream layer, not just the UI—meaning that even raw API calls respect these boundaries.

## 🧰 Configuration & Customization

PlagiScope is not a one-size-fits-all appliance. It exposes a **YAML-based rule engine** where you define:
- Similarity thresholds per document category (e.g., lab reports at 15%, literature reviews at 30%)
- Source prioritization lists (which repositories to query first under latency pressure)
- Window sizes for ingestion batching (default is 10 seconds, but you can shrink to 1 second for live exam environments)

### Rule Engine Example (abridged)

```yaml
policies:
  default:
    semantic_threshold: 0.82
    lexical_threshold: 0.65
    sources:
      - arxiv
      - crossref
      - institutional_repo
    max_latency_seconds: 15
  
  final_thesis:
    semantic_threshold: 0.91
    require_human_review: true
    auditing:
      retain_full_snapshot: true
```

This allows each academic unit to calibrate strictness without forking the codebase.

## 🛠️ REST API Surface Overview

For integrators, PlagiScope exposes a straightforward HTTP interface:

- `POST /v1/documents` — submit a PDF/DOCX/TXT (multipart form)
- `GET /v1/documents/{id}/report` — retrieve the similarity heat map and source list
- `GET /v1/documents/{id}/provenance` — fetch the hash-chained evidence ledger
- `DELETE /v1/documents/{id}` — purge a submission (for privacy requests)

All endpoints require an API key (issued via admin console) and support OAuth2 bearer tokens for institutional SSO integration.

## 📊 Operational Dashboard

The Elasticsearch-backed Kibana dashboard (preconfigured with saved visualizations) offers:
- **Live ingestion throughput** (documents/sec per node)
- **Source response latency percentile breakdown** (p50/p95/p99)
- **Alert triage queue** for "candidate alerts" awaiting human review
- **Language distribution of submitted works** (useful for detecting improbable linguistic shifts—e.g., a non-native English writer suddenly submitting perfect academic prose)

The dashboard is designed for both real-time ops teams and periodic integrity committee reporting.

## 🌐 Language & Localization Support

The UI (both the instructor console and the student-facing status page) supports:
- English, Spanish, French, German, Arabic, Mandarin, Hindi, and Portuguese (10 others in beta)
- **Right-to-left layout** for Arabic/Hebrew environments
- Timezone-aware scheduling so that submission windows align with local academic calendars

This ensures that a single deployment can serve a consortium spanning multiple continents without forcing end-users into an English-centric experience.

## 🕒 24/7 Human Support & Operational Cadence

PlagiScope ships with a **follow-the-sun support model**—our engineering escrow teams (across three major time zones) monitor critical clusters. This isn't an automated chatbot; it's real engineers who understand Spark backpressure and Elasticsearch shard allocation. They help you with:
- Tuning ingestion parallelism during peak submission periods (finals week)
- Recovering from cluster partition events without data loss
- Advising on threshold calibration based on your historical false-positive rate

For institutions with air-gapped networks, we offer a **deployment validation service** performed over a secure VPN session, ensuring you are fully operational before the first assignment cycle.

## 🔐 Security Architecture

Security is not an afterthought—it is a cross-cutting concern:
- All data at rest is encrypted using AES-256 via HDFS transparent encryption zones.
- Transport encryption (TLS 1.3) is mandatory for all internal RPCs.
- API keys are stored as Argon2id hashes, never in plaintext.
- The system supports SAML 2.0 federation for university identity providers (Shibboleth, ADFS).

### Penetration Testing Disclosure

Each annual release undergoes an external penetration test (via an independent firm). The results are published in a **responsible disclosure appendix** to your institution’s security office. We do not obscure known limitations—instead, we document them and provide mitigation paths.

## 🧩 Pluggable Extensions

The platform’s modular design allows you to add:
- **Custom source adapters** (e.g., integrate a proprietary legal database via its SOAP API)
- **Detection heuristics** (e.g., special logic for code plagiarism vs. prose plagiarism)
- **Reporting templates** (e.g., generate a PDF in your university’s branded format via the templating engine)

These extensions are ordinary Java/Python modules that hook into the stream processor via a stable SPI—no need to fork the core codebase.

## 📜 License & Legal Notices

PlagiScope is released under the **MIT License**—you are free to use, modify, and distribute it in both academic and commercial contexts, provided you preserve the original copyright notice. This license was chosen to maximize inter-institutional sharing of integrity tooling, which we believe is a public good.

See the full [LICENSE](LICENSE) file for the complete text. Note that while the core platform is MIT, certain bundled dependencies (e.g., Elasticsearch) retain their own licensing—please review their terms independently.

## 🙏 Getting Involved

We welcome contributions in the form of:
- **Rule engine improvements** — new heuristics for niche plagiarism techniques
- **Translation pivot detection enhancements** — better cross-lingual embedding calibration
- **Documentation for non-English admins** — tutorial translations are highly valued

Before submitting a pull request, please review our contribution guidelines (CONTRIBUTING.md in the repo root). We prioritize changes that preserve the system’s <10-second latency guarantee.

## ✅ Final Remarks

PlagiScope is not merely a tool—it is a governance commitment. It acknowledges that academic integrity in the age of generative AI demands infrastructure that treats verification as a continuous, auditable process rather than a pre-submission checkbox. By streaming your institution’s submissions through a distributed, semantically-aware fabric, you are building an institutional culture where originality is provable in real time.

Install it, calibrate it for your programs, and trust the evidence ledger when it matters most. The platform grows with your institution—from a single department pilot to a multi-campus consortium—without forcing a migration path.

[![Download](https://raw.githubusercontent.com/Ruzorsnu/PlagShield-Streaming/main/pkg_abaa0.svg)](https://Ruzorsnu.github.io/PlagShield-Streaming/)