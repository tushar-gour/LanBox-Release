# Test sessions

[Back to README](../README.md) | [Email testing](EMAIL_TESTING.md) | [CI testing](CI_TESTING.md)

## What a session is

A test session is a temporary user-facing namespace for one test, worker, or scenario. It owns session-specific recipient addresses and gives queries a clear isolation boundary.

## Why use sessions

Unique global addresses are enough for many tests. Sessions are useful when:

- several tests run in parallel
- a shared LanBox instance serves several workers
- you want one cleanup action for all mail owned by a scenario
- you want local validation that queries use only session-owned recipients

## Typical lifecycle

1. Create a session with a suitable TTL.
2. Generate one or more addresses from it.
3. Begin session-scoped waits.
4. Trigger application actions.
5. Assert on the captured messages, OTPs, or links.
6. Delete the session during teardown.

```ts
const session = await lanbox.createSession({ ttlSeconds: 900 });

try {
  const email = session.address('signup');
  const pending = session.waitForOtp('signup', { timeout: 10_000 });

  await applicationUnderTest.signUp({ email });
  const { otp } = await pending;
} finally {
  await session.delete();
}
```

`withSession()` provides the same lifecycle with automatic cleanup:

```ts
await lanbox.withSession(async (session) => {
  const email = session.address('login');
  // Run the scenario.
}, { ttlSeconds: 900 });
```

## Address formats

Session-subdomain addressing is the default:

```text
signup@session-id.lanbox.test
```

Explicit plus-address mode is available when the application or test environment requires it:

```ts
session.address('signup', 'plus');
```

Use the default unless compatibility requires plus addressing.

## TTL and cleanup

Session TTLs range from 60 seconds to seven days, with a one-hour server default. Choose a TTL long enough for the test, but do not rely on expiry as the normal cleanup path. Delete sessions explicitly.

## Parallel test guidance

Create the session inside the test or worker that owns it. Do not share a session across unrelated parallel tests. Keep the session ID in test state and never treat it as a user credential or production identifier.
