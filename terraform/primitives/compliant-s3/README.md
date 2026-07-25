# Compliant S3 Primitive

This module enforces SC-28, AU-3, AU-6, CM-6, AC-3 on a single S3 bucket.

It provisions a primary data bucket and a separate access-log bucket, both with
AES-256 server-side encryption at rest (SC-28), a full four-flag public access
block (AC-3), versioning and mandatory compliance tags applied via provider
default_tags (CM-6), and server access logging from the primary bucket into the
log bucket (AU-3 / AU-6). Compliance evidence is captured as machine-readable
JSON via `terraform show -json` rather than screenshots.
