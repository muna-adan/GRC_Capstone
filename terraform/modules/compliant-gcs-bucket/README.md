# compliant-gcs-bucket

A reusable Terraform module that provisions a Google Cloud Storage bucket with a
customer-managed encryption key (CMEK). Compliance controls are hardcoded in the
module body so consumers cannot disable them; only business config (project,
environment, retention, names) is exposed as variables.

## Controls enforced

- **SC-12** Cryptographic key establishment and management: customer-managed KMS key with 90-day automatic rotation.
- **SC-13** Cryptographic protection: CMEK used for bucket encryption.
- **SC-28** Protection of information at rest: all objects encrypted with the CMEK.
- **AU-11** Audit record retention: bucket retention policy holds objects for the configured period.
- **CM-6** Configuration settings: required compliance labels merged on top of any consumer labels and cannot be suppressed.

Additional posture: uniform bucket-level access and enforced public access
prevention (AC-3), plus object versioning.

## Evidence

The module emits a `compliance_attestation` output consumed by downstream
policy (Rego) and OSCAL labs. Captured evidence for the dev consumer lives in
`evidence/lab-2-4/`.
