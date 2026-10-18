# Security guidance

[Back to README](README.md)

LanBox is infrastructure for development and automated testing. It accepts SMTP messages and exposes captured content through a web interface and API. Treat every captured message, attachment, OTP, and link as potentially sensitive test data.

## Safe deployment

- Bind HTTP and SMTP ports to loopback for individual development machines.
- On shared networks, use firewall rules, private networking, and an authenticated reverse proxy or equivalent external access control.
- Do not expose an unprotected LanBox instance directly to the public internet.
- Use test-only recipients and test data. Do not send production secrets or customer data to LanBox.
- Keep PostgreSQL and filesystem data volumes private and backed up when persistence matters.
- Use separate instances or isolated sessions when teams or CI jobs should not see each other's test messages.
- Remove captured messages and expired environments when testing is complete.
- Pin a release tag such as `0.3.0` when reproducibility matters.

## Reporting a security issue

Do not open a public issue for a suspected vulnerability or include secrets, credentials, captured messages, or exploit details in public discussion.

Use GitHub's private vulnerability reporting feature for this repository when it is available. If it is unavailable, contact the maintainer through [https://diary.tushargour.com/](https://diary.tushargour.com/) and request a private reporting channel before sharing sensitive details.

Public issues are appropriate for documentation corrections and non-sensitive product behavior.

## Scope

This document provides user-facing operational guidance. It does not describe LanBox security internals.
