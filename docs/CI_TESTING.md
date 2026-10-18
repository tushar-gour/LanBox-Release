# CI testing

[Back to README](../README.md) | [Getting started](GETTING_STARTED.md) | [Test sessions](TEST_SESSIONS.md)

LanBox can run as a service inside a CI job so the application under test sends email to a local, controlled destination.

## Recommended flow

1. Start PostgreSQL 16 and `ghcr.io/tushar-gour/lanbox:0.3.0`.
2. Bind LanBox only inside the job network unless a test runner needs a host port.
3. Poll `/api/v1/ready` until it reports `ready`.
4. Configure the application SMTP host and port for the LanBox service.
5. Create unique addresses or one session per worker.
6. Register waits before actions that send mail.
7. Run the tests.
8. Delete sessions and remove the ephemeral environment.

## Generic shell example

The exact service startup syntax depends on your CI platform. Once LanBox is running:

```bash
until curl --fail --silent http://127.0.0.1:8025/api/v1/ready > /dev/null; do
  sleep 1
done

npx @lanbox/cli@0.3.0 status --json
npm test
```

Use a bounded outer job timeout so infrastructure failures do not wait forever.

## Parallel jobs

- Prefer a separate LanBox instance per isolated CI job.
- Within one shared instance, create one session per worker.
- Never let one worker use `clear-all` against shared data.
- Use checkpoints when a stable recipient must be reused.
- Pass `--json` to CLI commands when scripts need machine-readable output.

## Secrets and data

LanBox does not need production SMTP credentials. Keep production customer data and secrets out of generated test messages. Treat CI logs as public to the project team and do not print OTP values or message bodies unless the test output policy permits it.

## Failure diagnostics

On failure, collect only safe diagnostics:

- container status
- readiness response
- LanBox version
- test recipient address when it contains no secret
- structured error code
- sanitized container logs

Avoid publishing message contents, attachments, OTPs, tokens, or private links as CI artifacts.
