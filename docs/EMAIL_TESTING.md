# Email testing

[Back to README](../README.md) | [TypeScript SDK](SDK.md) | [Test sessions](TEST_SESSIONS.md)

## Core pattern

Use a unique recipient, begin the wait, trigger the action, assert on the result, and clean up data owned by the test.

```ts
const email = lanbox.generateAddress('scenario');
const pending = lanbox.wait(email, { timeout: 10_000 });

await applicationUnderTest.performAction({ email });
const message = await pending;
```

Starting the wait first prevents the test from depending on delivery timing.

## Signup email

1. Generate a new address.
2. Wait for a message with the expected signup subject.
3. Submit signup through the application.
4. Assert on sender, recipient, subject, and user-visible content.

Use a session when several workers perform signup at the same time.

## Verification code or OTP

```ts
const email = lanbox.generateAddress('verify');
const pending = lanbox.waitForOtp(email, {
  subject: 'Verify your account',
  bodyContains: 'verification code',
  length: 6,
  timeout: 10_000,
});

await applicationUnderTest.requestVerification(email);
const { otp } = await pending;
await applicationUnderTest.verifyEmail(email, otp);
```

If extraction is ambiguous, add filters or make the email template clearer. Do not select an arbitrary numeric value.

## Password reset or magic link

```ts
const email = lanbox.generateAddress('reset');
const pending = lanbox.waitForLink(email, {
  host: 'app.example.test',
  pathIncludes: '/reset-password',
  timeout: 10_000,
});

await applicationUnderTest.requestPasswordReset(email);
const { link } = await pending;
await browser.goto(link);
```

Filter links when templates include privacy, support, or unsubscribe URLs.

## Attachments

1. Wait for and retrieve the message.
2. Confirm the expected attachment metadata.
3. Download the attachment by message and attachment ID.
4. Validate its bytes, media type, or filename in the test.

Attachment access requires a capture mode that retains attachments.

## Avoid stale matches

Use a unique address for every test. When reusing an address is unavoidable, take a checkpoint before triggering mail:

```ts
const checkpoint = await lanbox.checkpoint();
const pending = lanbox.wait('shared@lanbox.test', {
  afterCursor: checkpoint,
  timeout: 10_000,
});
```

Do not use timestamps as a substitute for an exact checkpoint when deterministic ordering matters.

## Parallel workflows

- Create one session per test or worker.
- Generate addresses through that session.
- Avoid global clear operations in shared environments.
- Delete each session in teardown.
- Keep each wait scoped to its intended recipient and message attributes.

## Cleanup

Prefer session deletion or targeted message and inbox deletion. Use `clearAll(true)` only for an isolated instance whose complete contents belong to the caller.
