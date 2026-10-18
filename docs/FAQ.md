# FAQ

[Back to README](../README.md) | [Troubleshooting](TROUBLESHOOTING.md) | [Support](../SUPPORT.md)

## What is LanBox?

LanBox is local email infrastructure for development and automated testing. Applications send SMTP mail to LanBox, then developers and tests inspect the captured result through a browser, SDK, CLI, or API.

## Does LanBox send real emails?

No. LanBox captures email in the test environment and does not deliver it to the public internet.

## Is LanBox an SMTP provider?

No. It is not a production relay or outbound email provider.

## Can I use it in production?

Use LanBox for development, automated tests, CI, and controlled QA environments. Do not use it to deliver production email. If a shared environment is necessary, protect it with suitable network and external access controls.

## Can I use arbitrary `@lanbox.test` addresses?

Yes, when the instance recipient policy permits them. No mailbox creation is required. Expired or deleted session addresses are rejected.

## Can LanBox extract OTP codes?

Yes. It can read an OTP from existing mail or wait for an incoming message. Missing and ambiguous results are reported explicitly.

## Can LanBox extract magic links?

Yes. Link helpers can filter by host, path, URL content, or link text. LanBox returns links as text and does not visit them.

## Can I use it with Playwright?

Yes. Use the TypeScript client in the test runner, begin the email wait before the browser action, and continue the scenario with the returned OTP or link.

## Can I use it in CI?

Yes. Run the published container with PostgreSQL, wait for readiness, and use unique addresses or sessions. See [CI testing](CI_TESTING.md).

## Does it support parallel tests?

Yes. Use one isolated session or unique address per worker and avoid global cleanup in a shared instance.

## Is there a TypeScript SDK?

Yes. Install [@lanbox/client](https://www.npmjs.com/package/@lanbox/client). Version 0.3.0 has zero runtime dependencies and requires Node.js 22 or newer.

## Is there a CLI?

Yes. Install [@lanbox/cli](https://www.npmjs.com/package/@lanbox/cli). Version 0.3.0 depends exactly on `@lanbox/client@0.3.0`.

## What Node.js version is required?

Node.js 22 or newer is required for the TypeScript client and CLI.

## What PostgreSQL version is required?

PostgreSQL 16 or newer is required for LanBox deployments.

## What does search cover?

Search is a case-insensitive literal match across sender, recipients, and subject. It does not search bodies, HTML, attachments, filenames, or raw email.

## What is the difference between health and readiness?

`/api/v1/health` is a process liveness check. `/api/v1/ready` checks whether required dependencies are ready.

## Is the LanBox source public?

This repository contains public documentation and release information. LanBox implementation source is not distributed through this repository.

## Why is this repository documentation-only?

It gives users one public place for installation, guides, package links, container links, support, and release history without publishing private implementation details.
