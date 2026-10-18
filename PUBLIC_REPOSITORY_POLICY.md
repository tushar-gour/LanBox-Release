# Public repository policy

[Back to README](README.md)

> This repository contains public documentation and release information only. LanBox implementation details must not be published here.

## Allowed content

- Product descriptions and user guides
- Installation instructions using published distributions
- Public API behavior
- TypeScript client and CLI usage examples
- SMTP configuration examples
- Testing, CI, troubleshooting, and security guidance for users
- Public release notes and distribution links

## Prohibited content

- LanBox application source code
- Internal architecture, service topology, or dependency diagrams
- Private repository files, paths, links, issues, or pull requests
- Database schemas, SQL, migration implementation, or persistence design
- Internal CI configuration
- Internal tests, benchmarks, or chaos-test implementation
- Internal logging, metrics, queue, cache, retry, worker, storage, or networking design
- Secrets, credentials, tokens, private hostnames, or customer data
- Contribution or build instructions for the private implementation

Small consumer examples are welcome when they show how to use a published LanBox interface. Examples must not reproduce or explain how LanBox itself is implemented.

When there is uncertainty, omit implementation-specific detail and document the user-visible behavior instead.
