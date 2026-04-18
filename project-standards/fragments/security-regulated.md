<!-- tier: project -->
<!-- requires: security-generic -->

## Regulated Data — Additional Security Requirements

<!-- WHY: Regulatory violations carry fines, lawsuits, and loss of certifications. These rules exist because "best effort" isn't a legal defense. -->

### Data Classification

Before writing any code that touches user data, identify what you're handling:

| Classification | Examples | Handling |
|---------------|----------|----------|
| **PII** | Name, email, address, phone, DOB, SSN | Encrypt at rest and in transit, minimize collection, audit access |
| **PHI** | Medical records, diagnoses, prescriptions, insurance IDs | All PII rules + HIPAA-specific controls below |
| **PCI** | Card numbers, CVVs, expiration dates | All PII rules + PCI-DSS controls below |
| **Government** | Classified data, CUI, ITAR-controlled | Ask user for agency-specific handling guidelines — these vary too widely to template |

### Universal Rules for Regulated Data

- **Encrypt at rest.** All regulated data in databases, files, and backups must be encrypted (AES-256 or equivalent).
- **Encrypt in transit.** TLS 1.2+ for all connections. No exceptions, no fallbacks.
- **Minimize collection.** Only collect fields you need. Don't store data "just in case."
- **Audit logging.** Log every access to regulated data — who, when, what, from where. Logs themselves must not contain the regulated data.
- **Retention limits.** Define and enforce how long data is kept. Automate deletion when retention expires.
- **Access control.** Principle of least privilege. No shared accounts. No broad read access to regulated tables.

### HIPAA (PHI)

- Business Associate Agreements (BAAs) must be in place with all third-party services that touch PHI
- PHI must never appear in logs, error messages, or stack traces
- De-identification must follow Safe Harbor or Expert Determination methods
- Breach notification procedures must be documented and tested

### PCI-DSS (PCI)

- Never store CVV/CVC after authorization
- Card numbers must be masked in display (show last 4 only)
- Use a PCI-compliant payment processor — never handle raw card data if avoidable
- Quarterly vulnerability scans required
- Network segmentation between cardholder data environment and general systems

### Code Review Additions for Regulated Data

When reviewing code that touches regulated data, add these checks to the review gate:

- [ ] No regulated data in log output
- [ ] No regulated data in error messages or exceptions
- [ ] No regulated data in URLs or query parameters
- [ ] Encryption verified for storage and transmission
- [ ] Access control enforced at the data layer, not just the UI
- [ ] Data retention policy implemented (not just documented)
