# User guide

[Back to README](../README.md) | [Features](FEATURES.md) | [Public API](API.md)

## Inboxes and addresses

LanBox groups captured messages by recipient address. Under the default domain, addresses such as `signup@lanbox.test` and `worker-3@lanbox.test` are immediately usable when policy permits. No mailbox registration is required.

Use unique addresses for unrelated tests. Use a test session when you need stronger ownership and cleanup boundaries.

## Messages

Open the web UI at `http://127.0.0.1:8025` to browse messages. You can also use the SDK, CLI, or API to list, inspect, and delete mail.

Message details can include envelope recipients, sender, subject, timestamps, text, HTML, and attachments. Availability depends on the active capture mode.

## Search and pagination

Search is a case-insensitive literal match across sender, recipients, and subject. It does not search bodies, raw email, attachments, or filenames.

Use returned cursors exactly as received. A cursor marks an exclusive page boundary. Do not decode it, convert it to a number, or perform arithmetic on it.

## Attachments and raw email

The TypeScript client can download retained raw email and attachments as `Uint8Array` data. These operations are available only when the active capture mode retains the requested content.

Treat downloaded files as untrusted test input. Save or open them only when your test requires it.

## OTPs and links

Use OTP helpers to extract an unambiguous code from a matching message. Use link helpers with filters such as host or path when a message contains several links.

LanBox reports missing or ambiguous results instead of selecting an arbitrary value. Narrow the message query or extraction filters when this happens.

## Waiting for mail

Start the wait before triggering the application action:

```ts
const pending = lanbox.wait('receipt@lanbox.test', {
  subject: 'Payment received',
  timeout: 15_000,
});

await applicationUnderTest.sendReceipt();
const message = await pending;
```

When an address may contain older mail, create a checkpoint first and pass it as `afterCursor`.

## Deleting mail

Delete a single message, clear one inbox, or clear all messages in an isolated environment. The SDK requires explicit confirmation for the clear-all operation.

Avoid clearing shared environments from a test that does not own all captured mail.

## Test sessions

A session provides a temporary namespace for one test or worker. Generate addresses through the session, run the test, then delete the session during teardown.

Session-subdomain addressing is the default. Plus-address mode is available for systems that require that format.

## Operational checks

- `/api/v1/health` checks process liveness.
- `/api/v1/ready` reports dependency readiness.
- SDK `info()` returns public runtime configuration and capabilities.
- SDK `stats()` returns public operational statistics.

Use readiness checks before starting tests. Do not treat a liveness response as proof that all dependencies are ready.
