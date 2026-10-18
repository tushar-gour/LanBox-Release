# LanBox

**Local email infrastructure for development and automated testing.**

LanBox gives applications a safe SMTP destination during development, QA, and automated tests. It captures email locally, presents it in a browser inbox, and provides APIs, a TypeScript client, and a CLI for reliable email workflows.

Use LanBox to test real application email behavior without sending messages to customers, using personal inboxes, or creating test-only OTP bypasses.

Current stable release: **v0.3.0**

## Why LanBox

- Keep development email away from real users.
- Exercise the same SMTP path your application uses normally.
- Wait for messages, OTP codes, and magic links in automated tests.
- Give parallel tests isolated addresses and sessions.
- Inspect text, HTML, attachments, and raw email when the capture mode supports them.
- Search and paginate captured mail through the UI, SDK, CLI, or public API.
- Run locally, on a trusted network, or inside a CI environment.

LanBox is not a production outbound email provider. It captures mail for development and testing and does not deliver it to the public internet.

## Common use cases

- Signup and email verification
- OTP and two-factor authentication
- Password reset and passwordless login
- Magic-link authentication
- Transactional email and attachment checks
- Backend integration tests
- Browser end-to-end tests
- Parallel CI jobs and QA environments

See [Use cases](docs/USE_CASES.md) for practical workflows.

## Quick start

LanBox is distributed as a public container. PostgreSQL 16 or newer is required.

Create `compose.yaml`:

```yaml
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: lanbox
      POSTGRES_PASSWORD: lanbox
      POSTGRES_DB: lanbox
    volumes:
      - lanbox_postgres:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U lanbox -d lanbox"]
      interval: 5s
      timeout: 5s
      retries: 10

  lanbox:
    image: ghcr.io/tushar-gour/lanbox:0.3.0
    depends_on:
      postgres:
        condition: service_healthy
    environment:
      DATABASE_URL: postgresql://lanbox:lanbox@postgres:5432/lanbox
      HTTP_HOST: 0.0.0.0
      SMTP_HOST: 0.0.0.0
      SMTP_DOMAIN: lanbox.test
    ports:
      - "127.0.0.1:1025:1025"
      - "127.0.0.1:8025:8025"
    volumes:
      - lanbox_data:/app/data

volumes:
  lanbox_postgres:
  lanbox_data:
```

Start LanBox:

```bash
docker compose up -d
```

Open `http://127.0.0.1:8025`, then configure your application to use:

| Setting | Value |
| --- | --- |
| SMTP host | `127.0.0.1` |
| SMTP port | `1025` |
| TLS | off |
| Authentication | none |
| Recipient domain | `lanbox.test` |

Any allowed address under `lanbox.test`, such as `signup@lanbox.test`, can receive captured mail without mailbox provisioning.

The complete first-run walkthrough is in [Getting started](docs/GETTING_STARTED.md).

## TypeScript client

[![npm](https://img.shields.io/npm/v/@lanbox%2Fclient)](https://www.npmjs.com/package/@lanbox/client)

Node.js 22 or newer is required for npm tooling.

```bash
npm install --save-dev @lanbox/client@0.3.0
```

```ts
import { LanBox } from '@lanbox/client';

const lanbox = new LanBox();
const email = lanbox.generateAddress('signup');
const pendingOtp = lanbox.waitForOtp(email, { timeout: 10_000 });

await applicationUnderTest.signUp({ email });

const { otp } = await pendingOtp;
await applicationUnderTest.verifyEmail({ email, otp });
```

The client has zero runtime dependencies. See the [TypeScript SDK guide](docs/SDK.md).

## CLI

[![npm](https://img.shields.io/npm/v/@lanbox%2Fcli)](https://www.npmjs.com/package/@lanbox/cli)

```bash
npm install -g @lanbox/cli@0.3.0
lanbox status
lanbox address signup
lanbox wait-otp signup@lanbox.test --timeout 10000 --json
```

The v0.3.0 CLI depends exactly on `@lanbox/client@0.3.0`. See the [CLI guide](docs/CLI.md).

## Magic-link example

Register the wait before starting the application action that sends mail:

```ts
const pendingLink = lanbox.waitForLink('login@lanbox.test', {
  host: 'app.example.test',
  pathIncludes: '/magic-login',
  timeout: 10_000,
});

await applicationUnderTest.requestMagicLink('login@lanbox.test');
const { link } = await pendingLink;
```

LanBox returns the link as text. It does not open or execute it.

## Isolated test-session example

```ts
await lanbox.withSession(async (session) => {
  const email = session.address('reset');
  const pending = session.waitForLink('reset', {
    urlContains: '/reset-password',
    timeout: 10_000,
  });

  await applicationUnderTest.requestPasswordReset(email);
  const { link } = await pending;
  await browser.goto(link);
}, { ttlSeconds: 900 });
```

Sessions use session-subdomain addresses by default. Plus-address mode remains available when compatibility requires it.

## Documentation

- [Getting started](docs/GETTING_STARTED.md)
- [User guide](docs/USER_GUIDE.md)
- [Features](docs/FEATURES.md)
- [Use cases](docs/USE_CASES.md)
- [TypeScript SDK](docs/SDK.md)
- [CLI](docs/CLI.md)
- [Public API](docs/API.md)
- [Email testing](docs/EMAIL_TESTING.md)
- [Test sessions](docs/TEST_SESSIONS.md)
- [CI testing](docs/CI_TESTING.md)
- [Troubleshooting](docs/TROUBLESHOOTING.md)
- [FAQ](docs/FAQ.md)
- [Release history](docs/RELEASES.md)
- [Security guidance](SECURITY.md)
- [Support](SUPPORT.md)
- [Public repository policy](PUBLIC_REPOSITORY_POLICY.md)
- [Changelog](CHANGELOG.md)

## Distribution

- [@lanbox/client on npm](https://www.npmjs.com/package/@lanbox/client)
- [@lanbox/cli on npm](https://www.npmjs.com/package/@lanbox/cli)
- [LanBox container on GHCR](https://github.com/users/tushar-gour/packages/container/package/lanbox)
- [GitHub Releases](https://github.com/tushar-gour/LanBox-Release/releases)
- [Project website](https://diary.tushargour.com/)

## Security

LanBox is unauthenticated developer and testing infrastructure by default. Bind it to loopback or place it behind suitable network and access controls. Do not expose an unprotected instance directly to the public internet. Read [SECURITY.md](SECURITY.md) before using LanBox on a shared network.

## Support and license

Start with [Troubleshooting](docs/TROUBLESHOOTING.md) and the [FAQ](docs/FAQ.md). Public support guidance is available in [SUPPORT.md](SUPPORT.md).

This public documentation repository is licensed under the [MIT License](LICENSE).
