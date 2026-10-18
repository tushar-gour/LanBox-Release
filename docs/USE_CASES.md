# Use cases

[Back to README](../README.md) | [Features](FEATURES.md) | [Email testing](EMAIL_TESTING.md)

## Local development

**Problem:** Sending development mail to real inboxes is slow and risky.

**Workflow:** Point local SMTP settings at LanBox, trigger an application action, and inspect the result in the browser inbox.

**How LanBox helps:** Messages stay in the development environment and are available immediately.

**Benefit:** Faster feedback without customer delivery or personal mailbox credentials.

## Signup and email verification

**Problem:** A new account cannot complete signup until a code or link is received.

**Workflow:** Generate a unique address, begin an OTP or link wait, submit signup, then complete verification with the returned value.

**How LanBox helps:** The test reads the actual message produced by the application.

**Benefit:** The complete verification flow is exercised without a bypass.

## OTP authentication

**Problem:** Manually copying codes prevents reliable automation.

**Workflow:** Wait for an OTP with the expected recipient, subject, and length, then submit it to the application.

**How LanBox helps:** It returns a structured OTP result or a clear missing or ambiguous error.

**Benefit:** Authentication tests remain realistic and deterministic.

## Password reset and magic-link login

**Problem:** Tests need the exact link generated for one request.

**Workflow:** Begin a filtered link wait, request the email, then navigate the browser to the extracted URL.

**How LanBox helps:** Host and path filters select the intended link without visiting it automatically.

**Benefit:** The application controls link generation while the test controls navigation.

## Transactional email

**Problem:** Receipts, notices, and other messages need content and recipient checks.

**Workflow:** Send the transaction, wait for mail, then inspect the sender, recipients, subject, text, HTML, or attachment metadata.

**How LanBox helps:** The message is available through the browser, SDK, CLI, and API.

**Benefit:** Email remains part of the tested user journey.

## Backend integration tests

**Problem:** Mocking SMTP can hide configuration and rendering failures.

**Workflow:** Run LanBox beside the application and database, use a unique recipient, and assert on the captured result.

**How LanBox helps:** The application performs a real SMTP transaction inside the test environment.

**Benefit:** Tests cover more of the actual integration boundary.

## End-to-end browser tests

**Problem:** Browser flows often leave the application to retrieve a code or link.

**Workflow:** Use the TypeScript client from the test runner, register the wait, perform the browser action, and continue with the result.

**How LanBox helps:** Email becomes another automatable step in the scenario.

**Benefit:** Signup, recovery, and passwordless flows run unattended.

## CI pipelines

**Problem:** External mail delivery adds latency, credentials, and flaky dependencies.

**Workflow:** Start the published container with PostgreSQL, wait for readiness, run tests, and remove the environment.

**How LanBox helps:** All email remains inside the CI network.

**Benefit:** Repeatable jobs with no production SMTP secret.

## Mobile-app backend testing

**Problem:** A mobile backend may send codes or links even when the mobile UI is tested separately.

**Workflow:** Configure the backend SMTP destination for the test environment and retrieve results through the SDK or API.

**How LanBox helps:** The backend email contract can be verified independently of a real mailbox.

**Benefit:** Faster backend validation and easier automated setup.

## Parallel test suites

**Problem:** Shared recipients let workers consume or match each other's messages.

**Workflow:** Create one session per worker or test, use session-specific addresses, and delete the session during teardown.

**How LanBox helps:** Queries remain scoped to the owning session.

**Benefit:** Fewer cross-test collisions and clearer cleanup.

## QA environments

**Problem:** QA teams need to inspect email without receiving it personally.

**Workflow:** Run LanBox on a trusted network with external access controls and assign scenario-specific test addresses.

**How LanBox helps:** Browser inspection and automation can share the same controlled environment.

**Benefit:** Repeatable manual and automated verification.

## Attachment verification

**Problem:** Tests need to confirm that a generated file was attached correctly.

**Workflow:** Wait for the message, inspect attachment metadata, download the bytes, and validate the file in the test.

**How LanBox helps:** Attachment access is available when the configured capture mode retains files.

**Benefit:** The test can validate both message delivery and generated documents.
