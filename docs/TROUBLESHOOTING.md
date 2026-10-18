# Troubleshooting

[Back to README](../README.md) | [Getting started](GETTING_STARTED.md) | [FAQ](FAQ.md)

## The application cannot connect to SMTP

- Confirm the LanBox container is running.
- Confirm the application uses port `1025`.
- From the host, use `127.0.0.1`. From another container, use the LanBox service name.
- Disable SMTP TLS and authentication unless your surrounding network adds them externally.
- Check firewall and container network rules.

## An email does not appear

- Confirm the application completed its send operation.
- Check that the recipient uses the configured domain, normally `lanbox.test`.
- Verify readiness at `/api/v1/ready`.
- Confirm the message is within the configured size limit.
- Check whether a session address has expired or was deleted.
- Inspect sanitized application and LanBox logs for a structured error.

## The web UI does not open

- Check `docker compose ps`.
- Confirm port `8025` is published to the expected interface.
- Open `http://127.0.0.1:8025` rather than the SMTP port.
- Check whether another process already uses port `8025`.

## PostgreSQL is unavailable or readiness fails

- Confirm PostgreSQL 16 or newer is healthy.
- Check the `DATABASE_URL` supplied to the LanBox container.
- Confirm both services share a Docker network.
- Wait for the database health check before starting tests.
- Keep the database volume writable and ensure the host has free disk space.

## OTP not detected

- Confirm the message contains the code in retained text.
- Provide the expected OTP length.
- Narrow the query with recipient, sender, subject, or body text.
- If several candidates have equal confidence, update the template or filters rather than accepting an arbitrary code.
- Confirm the capture mode supports OTP extraction.

## Magic link not detected

- Confirm the message contains an absolute link in retained content.
- Add `host`, `pathIncludes`, `urlContains`, or `linkText` filters.
- Check whether several links match equally.
- Confirm the capture mode supports link extraction.

## A wait times out

- Begin the wait before triggering the action that sends mail.
- Confirm the recipient and filters match the generated message.
- Increase the timeout only after checking delivery and readiness.
- Use a checkpoint when reusing an address.
- Do not rely on a fixed sleep in place of a wait.

## The wrong or stale message matched

- Use a unique address per test.
- Create a checkpoint before the action and pass it as `afterCursor`.
- Add sender, subject, or body filters.
- Use one session per parallel test worker.

## Parallel tests interfere with each other

- Do not share recipient addresses.
- Create one test session per worker or scenario.
- Avoid global deletion from parallel tests.
- Delete only the session, inbox, or message owned by the test.

## Raw email or attachment download is unavailable

The active capture mode may not retain the requested content. Check the public runtime information and use a capture mode appropriate for the test. Unsupported operations return `CAPTURE_MODE_UNSUPPORTED`.

## The CLI command is not found

- Confirm Node.js 22 or newer is installed.
- Reinstall with `npm install -g @lanbox/cli@0.3.0`.
- Use `npx @lanbox/cli@0.3.0 --version` if global binaries are not on `PATH`.

## Container platform problems

The published image supports `linux/amd64` and `linux/arm64`. Confirm Docker is using a supported Linux container platform and can pull from GHCR.

## Still blocked

Read [SUPPORT.md](../SUPPORT.md) before opening a public issue. Remove secrets and captured content from all reports.
