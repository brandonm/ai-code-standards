<!-- tier: project -->
<!-- requires: none -->

## Data Privacy & Handling

<!-- WHY: Privacy violations erode user trust and trigger regulatory action (GDPR fines, CCPA penalties). Building privacy in from the start is cheaper than retrofitting after a breach. -->

### Data Handling Principles

1. **Collect only what you need.** Every field collected must have a documented purpose. "We might need it later" is not a purpose.

2. **Separate PII from operational data.** Store personally identifiable information in dedicated, access-controlled tables — not mixed into general-purpose tables.

3. **Anonymize for development.** Never use production PII in development or staging environments. Use synthetic data or anonymized copies.

4. **Default to private.** New fields and endpoints should be private by default. Expose data explicitly, not accidentally.

### Retention & Deletion

- Define retention periods for every data category at design time
- Implement automated deletion — don't rely on manual cleanup
- Support user deletion requests (right to be forgotten) — cascading deletes must remove data from all stores including backups, caches, and search indices
- Soft deletes are acceptable for audit trails, but PII within soft-deleted records must be scrubbed after the retention window

### Logging & Observability

- **Never log PII.** No names, emails, addresses, phone numbers, IPs (unless required for security), or session tokens in log output.
- Use opaque identifiers (user IDs, request IDs) instead of PII in logs
- If you must log PII for debugging, use a separate short-retention debug log with restricted access
- Sanitize error messages and stack traces before they reach logging infrastructure

### Third-Party Data Sharing

- Inventory all third-party services that receive user data
- Every data-sharing integration must have a documented purpose and legal basis
- Analytics and tracking must respect user consent (opt-in, not opt-out)
- Review third-party SDKs for undisclosed data collection
