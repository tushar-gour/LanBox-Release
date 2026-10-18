# TypeScript SDK

[Back to README](../README.md) | [Email testing](EMAIL_TESTING.md) | [Test sessions](TEST_SESSIONS.md)

Package: [@lanbox/client](https://www.npmjs.com/package/@lanbox/client)

Current stable version: `0.3.0`

Requirements: Node.js 22 or newer. The client has zero runtime dependencies and uses the native platform fetch implementation.

## Install

```bash
npm install --save-dev @lanbox/client@0.3.0
```

## Create a client

```ts
import { LanBox } from '@lanbox/client';

const lanbox = new LanBox();
```

The default API URL is `http://127.0.0.1:8025/api/v1` and the default recipient domain is `lanbox.test`.

```ts
const lanbox = new LanBox({
  baseUrl: 'http://192.168.1.20:8025/api/v1',
  domain: 'mail.example.test',
});
```

## Generate an address

```ts
const email = lanbox.generateAddress('signup');
```

The address is generated locally and is suitable for independent test runs.

## Wait for a message

```ts
const email = lanbox.generateAddress('receipt');
const pending = lanbox.wait(email, {
  from: 'billing@example.test',
  subject: 'Payment received',
  timeout: 15_000,
});

await applicationUnderTest.sendReceipt(email);
const message = await pending;
```

Available query filters include `from`, `subject`, `bodyContains`, `after`, and `afterCursor`. Wait operations also accept `timeout` and `signal`.

## Wait for an OTP

```ts
const email = lanbox.generateAddress('verify');
const pending = lanbox.waitForOtp(email, {
  subject: 'Verify your account',
  length: 6,
  timeout: 10_000,
});

await applicationUnderTest.requestVerification(email);
const { otp, messageId } = await pending;
```

Use `otp()` when the message is already captured. Missing or ambiguous codes produce a structured `LanBoxOtpError`.

## Wait for a magic link

```ts
const pending = lanbox.waitForLink('login@lanbox.test', {
  host: 'app.example.test',
  pathIncludes: '/magic-login',
  linkText: 'Sign in',
  timeout: 10_000,
});

await applicationUnderTest.requestMagicLink('login@lanbox.test');
const { link } = await pending;
```

Filters include `host`, `pathIncludes`, `urlContains`, and `linkText`. LanBox returns the link as text and does not fetch it.

## Checkpoints

Use a checkpoint when a recipient might already contain old mail:

```ts
const checkpoint = await lanbox.checkpoint();
const pending = lanbox.wait('billing@lanbox.test', {
  afterCursor: checkpoint,
  timeout: 10_000,
});

await applicationUnderTest.sendReceipt();
const message = await pending;
```

Keep checkpoint values as strings and send them back unchanged.

## Test sessions

```ts
await lanbox.withSession(async (session) => {
  const email = session.address('signup');
  const pending = session.waitForOtp('signup', { timeout: 10_000 });

  await applicationUnderTest.signUp({ email });
  const { otp } = await pending;
  await applicationUnderTest.verifyEmail({ email, otp });
}, { ttlSeconds: 900 });
```

`withSession()` creates and cleans up the session. For manual ownership:

```ts
const session = await lanbox.createSession({ ttlSeconds: 900 });
try {
  const email = session.address('reset');
  // Run the test with this address.
} finally {
  await session.delete();
}
```

The default format is `prefix@session-id.domain`. Use `session.address('signup', 'plus')` only when plus-address compatibility is required.

## Messages, search, and pagination

```ts
let cursor: string | undefined;

do {
  const page = await lanbox.listMessages({
    search: 'signup',
    limit: 25,
    cursor,
  });

  for (const message of page.messages) {
    console.log(message.subject);
  }

  cursor = page.nextCursor;
} while (cursor !== undefined);
```

Search is case-insensitive literal metadata search across sender, recipients, and subject. Cursors are opaque strings. Return `nextCursor` unchanged and stop when it is absent.

Useful methods include:

- `listInboxes()`
- `listMessages()`
- `listInboxMessages()`
- `getMessage()`
- `deleteMessage()`
- `clearInbox()`
- `clearAll(true)`

## Raw email and attachments

```ts
const detail = await lanbox.getMessage('message-id');
const raw = await lanbox.downloadRawMessage(detail.id);
const attachment = await lanbox.downloadAttachment(
  detail.id,
  detail.attachments[0].id,
);
```

Downloads return `Uint8Array` content. Reduced capture modes can reject unsupported downloads with code `CAPTURE_MODE_UNSUPPORTED`.

## Readiness and runtime information

```ts
const health = await lanbox.status();
const readiness = await lanbox.ready();
const info = await lanbox.info();
const stats = await lanbox.stats();
```

`status()` is a liveness check. `ready()` returns either `ready` or `not_ready` as a typed result.

## Cancellation and errors

```ts
import { LanBoxTimeoutError } from '@lanbox/client';

try {
  await lanbox.waitForOtp('signup@lanbox.test', { timeout: 5_000 });
} catch (error) {
  if (error instanceof LanBoxTimeoutError) {
    throw new Error('Verification email did not arrive', { cause: error });
  }
  throw error;
}
```

LanBox provides structured error classes for HTTP, protocol, timeout, abort, OTP, link, session, and assertion failures. Pass an `AbortSignal` when the surrounding test owns cancellation.

Ordinary safe reads may retry transient network failures and HTTP 503 responses. Waits and destructive requests are not automatically retried.
