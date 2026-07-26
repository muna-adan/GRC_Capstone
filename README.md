# GRC_Capstone

**Compliance as code.** This repository builds a library of small, reusable cloud security *primitives* where every control is enforced directly in Terraform and proven with machine-readable evidence instead of screenshots.

Each primitive maps to specific NIST 800-53 controls, deploys to AWS, and produces a JSON evidence bundle that a policy engine (or a human auditor) can verify automatically.

---

## The idea

Traditional GRC proves compliance by hand: someone configures a resource by clicking through a console, takes screenshots, and files them for the auditor. That approach is slow, easy to fake, and drifts out of date the moment a setting changes.

This capstone takes the GRC engineering approach instead. Controls are written as infrastructure code, each one labeled with the NIST control it satisfies at the exact line where it's enforced. The proof is the `terraform show -json` output, a machine-readable artifact that stands on its own. No screenshots, version-controlled, and re-verifiable on demand.

The result is compliance that can be read, tested, and continuously evaluated like any other code.

---

## Repository structure

```
GRC_Capstone/
├── terraform/
│   └── primitives/
│       └── compliant-s3/      # Lab 2.3 — S3 enforcing SC-28, AU-3, AU-6, CM-6, AC-3
│           ├── main.tf
│           ├── variables.tf
│           ├── outputs.tf
│           └── README.md
├── evidence/
│   └── lab-2-3/               # machine-readable compliance evidence
│       ├── plan.json
│       └── state.json
└── README.md
```

Every primitive follows the same shape: a Terraform module under `terraform/primitives/`, and its captured evidence under `evidence/`. By the end of the course this becomes a dozen composable primitives plus their proofs.

---

## Course roadmap

Eleven labs that build in layers. Early chapters produce compliant infrastructure; middle chapters wrap it in policy-as-code and signed CI pipelines; later chapters stand up cloud security baselines and formal OSCAL audit artifacts. Everything culminates in a capstone that governs a real API under a 30-day deadline.

| Lab | Focus | Stack | Status |
|-----|-------|-------|--------|
| **2.3** Building Your First Compliant Resource | S3 bucket satisfying SC-28, AU-3, AU-6, CM-6, AC-3 with machine-readable evidence | Terraform, AWS, NIST 800-53 | ✅ Complete |
| **2.4** Terraform Modules for Compliance | GCP module hardcoding SC-12, SC-13, SC-28, AU-11, CM-6 so consumers can't disable controls | Terraform, GCP, KMS | ✅ Complete |
| **2.5** IaC as Compliance Evidence | S3 Object Lock vault that refuses deletion by design; bundle, hash, and upload evidence with a recorded VersionId | Terraform, AWS, Object Lock | ✅ Complete |
| **3.3** Writing Compliance Policies in Rego | Three Rego policies against GCP fixtures, each mapped to a control, with test fixtures and a real `terraform plan -json` run | OPA, Rego, GCP | Planned |
| **3.4** Integrating PaC with Terraform | Conftest wired into the plan workflow as a fail-closed gate; AWS SC-28 and AC-3 policy variants; a proven blocked merge | Conftest, OPA, AWS | Planned |
| **4.3** Building a GRC Evidence Pipeline | GitHub Actions running plan + Conftest + tfsec on every PR via AWS OIDC, failing closed on high-severity findings | GitHub Actions, AWS, OIDC | Planned |
| **4.4** Evidence Management & Chain of Custody | Keyless Cosign signing via GitHub OIDC, signed bundles pushed to the vault, full chain verified end to end | Cosign, Sigstore, AWS | Planned |
| **5.2** AWS Security Services Baseline | Multi-region CloudTrail with log-file validation + Security Hub on NIST 800-53 Rev 5, findings pulled as JSON evidence | AWS, CloudTrail, Security Hub | Planned |
| **5.4** GCP Security Services Baseline | GCP Org Policy enforced at the API, service account keys replaced with Workload Identity Federation, Data Access audit logs enabled | GCP, Org Policy, WIF | Planned |
| **6.1** Introduction to OSCAL | Valid OSCAL Component Definition for a Terraform module, evidence URIs wired to vault objects, validated with `trestle` | OSCAL, NIST | Planned |
| **7** Capstone: Acme Health | First GRC engineer making a Patient Intake API audit-defensible in 30 days without slowing engineering down | Capstone, portfolio | Planned |

---

## Completed work

### Lab 2.3 — Compliant S3 Primitive

A single S3 bucket (plus a separate access-log bucket) enforcing five NIST 800-53 controls:

| Control | Requirement | Enforced by |
|---------|-------------|-------------|
| **SC-28** | Protection of information at rest | AES-256 server-side encryption |
| **AC-3** | Access enforcement | Full four-flag public access block |
| **CM-6** | Configuration settings | Versioning + mandatory compliance tags via provider `default_tags` |
| **AU-3** | Content of audit records | Server access logging to a dedicated log bucket |
| **AU-6** | Audit review | Audit trail stored separately from the data it records |

Evidence is captured in [`evidence/lab-2-3/`](evidence/lab-2-3/). The `state.json` output confirms each control directly: the SSE algorithm for SC-28, the four public-access flags for AC-3, the compliance tags for CM-6, and the logging target for AU-3.

Verification was also confirmed live against the deployed bucket via `aws s3api` calls, matching the declared state.

---

## Tools and standards

- **Terraform** — infrastructure and controls as code, across both AWS and GCP
- **AWS** — S3, Object Lock, IAM, CloudTrail, Security Hub, OIDC trust
- **GCP** — KMS, Org Policy, Workload Identity Federation, Data Access audit logs
- **NIST 800-53 (Rev 5)** — the control catalog every primitive maps to
- **OPA / Rego + Conftest** — policy-as-code, run as fail-closed gates on Terraform plans *(Ch 3–4)*
- **GitHub Actions** — CI pipelines producing a named evidence artifact per run *(Ch 4)*
- **Cosign / Sigstore** — keyless signing for evidence chain of custody *(Ch 4)*
- **tfsec** — static analysis of Terraform for security findings *(Ch 4)*
- **OSCAL + trestle** — machine-readable control-to-evidence linkage for assessors *(Ch 6)*

---
