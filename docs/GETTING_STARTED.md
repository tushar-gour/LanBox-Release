# Getting started

[Back to README](../README.md) | [User guide](USER_GUIDE.md) | [Troubleshooting](TROUBLESHOOTING.md)

This guide takes you from an empty machine to your first captured message and automated OTP or magic-link check. You do not need access to LanBox source code.

## 1. Requirements

- Docker with Compose support
- PostgreSQL 16 or newer, provided by the example Compose file
- Node.js 22 or newer only when using the TypeScript client or CLI

## 2. Start LanBox

Copy the consumer-focused `compose.yaml` from the [README quick start](../README.md#quick-start), then run:

```bash
docker compose up -d
docker compose ps
```

Use a fixed release tag such as `ghcr.io/tushar-gour/lanbox:0.3.0` for repeatable environments.

Check liveness and readiness:

```bash
curl http://127.0.0.1:8025/api/v1/health
curl http://127.0.0.1:8025/api/v1/ready
```

`health` answers whether the process is running. `ready` answers whether the instance is ready to serve requests with its required dependencies.

## 3. Configure your application

Point the application SMTP configuration at:

```text
host: 127.0.0.1
port: 1025
secure/TLS: false
authentication: none
```

Use a recipient such as `first-run@lanbox.test`.

If the application runs in another container, use the LanBox Compose service name instead of `127.0.0.1`. Keep the port at `1025`.

## 4. Send and inspect a message

Trigger the application action that sends email. Open the browser inbox at `http://127.0.0.1:8025`, choose the matching inbox, and inspect the message. Depending on the configured capture mode, LanBox can present message text, HTML, attachments, or raw email.

## 5. Add the TypeScript client

```bash
npm install --save-dev @lanbox/client@0.3.0
```

```ts
import { LanBox } from '@lanbox/client';

const lanbox = new LanBox();
const email = lanbox.generateAddress('signup');
const pending = lanbox.wait(email, { subject: 'Welcome', timeout: 10_000 });

await applicationUnderTest.signUp({ email });
const message = await pending;
console.log(message.subject);
```

Register the wait before the action that sends mail. This avoids a timing race in the test.

## 6. Try the CLI

```bash
npm install -g @lanbox/cli@0.3.0
lanbox status
lanbox address first-run
lanbox inboxes
```

Use `--json` in scripts and CI jobs.

## 7. Try an OTP flow

```ts
const email = lanbox.generateAddress('otp');
const pending = lanbox.waitForOtp(email, { length: 6, timeout: 10_000 });

await applicationUnderTest.requestOtp(email);
const { otp } = await pending;
```

If a message can contain several numeric values, narrow the query by subject or body text and specify the expected length.

## 8. Try a magic-link flow

```ts
const email = lanbox.generateAddress('login');
const pending = lanbox.waitForLink(email, {
  host: 'app.example.test',
  pathIncludes: '/magic-login',
  timeout: 10_000,
});

await applicationUnderTest.requestMagicLink(email);
const { link } = await pending;
```

LanBox extracts the link but never visits it.

## 9. Next steps

- Learn the browser and message workflows in the [User guide](USER_GUIDE.md).
- Build deterministic tests with [Email testing](EMAIL_TESTING.md).
- Isolate parallel jobs with [Test sessions](TEST_SESSIONS.md).
- Review [Security guidance](../SECURITY.md) before sharing an instance.
