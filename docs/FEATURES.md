# Features

[Back to README](../README.md) | [Use cases](USE_CASES.md) | [Email testing](EMAIL_TESTING.md)

## Local email capture

**What it does:** Accepts application SMTP traffic in a controlled development or test environment.

**Why it matters:** Developers exercise the real email-sending path without delivering messages to customers or personal accounts.

**Common use cases:** Signup, password reset, notifications, transactional email, and template review.

**Benefits:** Safer testing, faster feedback, and fewer external service dependencies.

## Browser inbox

**What it does:** Provides a web interface for browsing inboxes and inspecting captured messages.

**Why it matters:** Humans can review email content during development without switching to an external mailbox.

**Common use cases:** Template review, link inspection, attachment checks, and manual QA.

**Benefits:** One local place for test email with no mailbox setup.

## Virtual test addresses

**What it does:** Allows arbitrary addresses under the configured test domain when policy permits.

**Why it matters:** Tests do not need pre-created accounts or shared inbox credentials.

**Common use cases:** Per-test recipients, per-worker addresses, and readable scenario names.

**Benefits:** Simple setup and fewer collisions between test runs.

## OTP extraction

**What it does:** Finds a deterministic OTP result from matching mail and reports missing or ambiguous codes.

**Why it matters:** Tests can validate the real authentication flow instead of adding a test-only bypass.

**Common use cases:** Email verification, two-factor authentication, and account recovery.

**Benefits:** Less manual copying and more realistic automation.

## Magic-link extraction

**What it does:** Extracts links and supports host, path, URL, and link-text filters.

**Why it matters:** Tests can follow the link the application actually generated.

**Common use cases:** Passwordless login, verification, invitations, and password reset.

**Benefits:** Reliable selection without executing links inside LanBox.

## Wait for email

**What it does:** Waits for a message that matches recipient and optional sender, subject, body, time, or checkpoint criteria.

**Why it matters:** Tests do not need fixed sleeps or repeated mailbox polling.

**Common use cases:** Integration tests, browser tests, and CI jobs.

**Benefits:** Faster tests with clearer timeouts and fewer timing races.

## TypeScript SDK

**What it does:** Provides typed operations for health, readiness, messages, sessions, waits, OTPs, links, downloads, and cleanup.

**Why it matters:** Tests can use LanBox through a stable consumer contract with runtime response validation.

**Common use cases:** Vitest, Jest, Playwright, and custom Node.js test runners.

**Benefits:** Typed results, structured errors, cancellation support, and zero runtime dependencies.

## CLI

**What it does:** Provides terminal commands for status, addresses, inboxes, messages, OTPs, links, cleanup, and sessions.

**Why it matters:** Shell scripts and CI jobs can use LanBox without writing a Node.js program.

**Common use cases:** Smoke tests, setup scripts, diagnostics, and manual checks.

**Benefits:** Human-readable output and compact JSON for automation.

## Parallel test isolation and sessions

**What it does:** Gives each test or worker a temporary session namespace and session-specific addresses.

**Why it matters:** Parallel tests should not match each other's messages.

**Common use cases:** Sharded browser tests, parallel integration tests, and shared QA instances.

**Benefits:** Cleaner ownership, scoped queries, and deliberate teardown.

## Message inspection and deletion

**What it does:** Lists messages, returns message details, and deletes messages or inbox contents.

**Why it matters:** Tests can assert delivery details and clean up only the data they own.

**Common use cases:** Subject and recipient checks, template assertions, and test teardown.

**Benefits:** Better diagnostics and predictable environment hygiene.

## Raw email and attachments

**What it does:** Returns retained raw email and attachment bytes when the capture mode supports them.

**Why it matters:** Tests can inspect MIME output and verify attached files.

**Common use cases:** Invoice PDFs, calendar files, inline assets, and mail-client compatibility checks.

**Benefits:** Coverage beyond plain text and subject assertions.

## Search and pagination

**What it does:** Searches message metadata and traverses large collections using opaque cursors.

**Why it matters:** Busy test environments remain usable without loading every message at once.

**Common use cases:** Finding a recipient, sender, or subject and processing inboxes page by page.

**Benefits:** Predictable collection access for both humans and automation.

## Health and readiness

**What it does:** Separates process liveness from dependency readiness.

**Why it matters:** Automation can wait until LanBox is ready before running email tests.

**Common use cases:** Container orchestration, CI setup, and local diagnostics.

**Benefits:** Clearer startup checks and failure reporting.

## CI integration and developer safety

**What it does:** Combines published containers, deterministic waits, unique addresses, sessions, and machine-readable CLI output.

**Why it matters:** Email workflows can be tested without production SMTP credentials or external inboxes.

**Common use cases:** Pull-request validation, nightly suites, and ephemeral environments.

**Benefits:** Realistic email testing with controlled local data and repeatable setup.

## Why not Gmail or heavy mocking?

Personal mailboxes introduce credentials, delivery delays, rate limits, shared state, and cleanup work. Heavy mocks often skip the application's real SMTP behavior and generated message content. LanBox provides a middle ground: the application still sends email normally, while tests receive and inspect it in a controlled environment.
