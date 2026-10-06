# Security notes

Synapse handles accounts, study records and user-uploaded PDFs. The current implementation therefore treats authorization and file handling as first-class design concerns.

## Implemented controls

- Authentication is checked on protected pages and API routes.
- User identity is derived from the authenticated session rather than accepted from request bodies.
- Queries for user-owned records include an explicit ownership predicate.
- PostgreSQL Row Level Security provides a database-level boundary in addition to application checks.
- Service-role access is kept in server-only code.
- Uploaded PDFs are checked for the `%PDF` file signature and limited to 20 MB.
- Storage paths are namespaced by authenticated user ID.
- AI marking verifies file ownership before server-side retrieval.
- AI results are validated against runtime schemas before persistence.
- Expensive marking requests are limited per user.
- Friends can read study-duration data used by social features, not private diary reflections.
- Authentication cookies are managed through the server-side Supabase integration.

## Threats considered

| Threat | Mitigation |
|---|---|
| Insecure direct object reference | Authenticated ownership filters and RLS |
| Malicious or mislabeled upload | Magic-byte and size validation; private storage |
| Leaked service credential | Server-only administrative client and ignored environment files |
| Untrusted model response | Schema validation before database writes |
| Abuse of paid model endpoint | Per-user rate limiting |
| Cross-user social data exposure | Narrow RLS policy exposing duration fields only to accepted friends |

## Known follow-up work

- Add automated authorization tests for every user-owned table and route.
- Add content-security-policy and security-header regression tests.
- Add audit events for sensitive file and marking operations without logging document contents.
- Add malware scanning if additional file types are accepted.
- Add retention and account-deletion workflows before wider release.
- Commission an independent security review before handling production student data at scale.

## Reporting

> **Placeholder:** add a private security contact before public deployment. Do not ask reporters to post vulnerabilities in public issues.
