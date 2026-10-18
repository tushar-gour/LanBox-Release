# CLI

[Back to README](../README.md) | [Getting started](GETTING_STARTED.md) | [CI testing](CI_TESTING.md)

Package: [@lanbox/cli](https://www.npmjs.com/package/@lanbox/cli)

Current stable version: `0.3.0`

The CLI requires Node.js 22 or newer and depends exactly on `@lanbox/client@0.3.0`.

## Install or run directly

```bash
npm install -g @lanbox/cli@0.3.0
lanbox --version
```

Or run without a global installation:

```bash
npx @lanbox/cli@0.3.0 status
```

## Common commands

| Command | Purpose |
| --- | --- |
| `lanbox status` | Check API, database, and SMTP status |
| `lanbox open` | Open the browser inbox |
| `lanbox address [prefix]` | Generate a collision-resistant test address |
| `lanbox inboxes` | List inboxes with search and pagination |
| `lanbox latest <address>` | Read the latest matching message |
| `lanbox wait <address>` | Wait for a matching message |
| `lanbox otp <address>` | Extract an OTP from existing mail |
| `lanbox wait-otp <address>` | Wait for and extract an OTP |
| `lanbox link <address>` | Extract a link from existing mail |
| `lanbox wait-link <address>` | Wait for and extract a link |
| `lanbox clear <address>` | Delete one inbox |
| `lanbox clear-all --confirm` | Delete all captured messages |
| `lanbox session ...` | Create, inspect, address, or delete sessions |

## Addresses and messages

```bash
lanbox address signup
lanbox latest user@lanbox.test --from notifications@example.test
lanbox wait signup@lanbox.test --subject "Welcome" --timeout 10000
```

## OTP and link workflows

```bash
lanbox otp user@lanbox.test --length 6
lanbox wait-otp signup@lanbox.test --length 6 --timeout 10000 --json
```

```bash
lanbox link user@lanbox.test --host app.example.test --path-includes /verify
lanbox wait-link signup@lanbox.test --host app.example.test --timeout 10000 --json
```

Begin a wait before triggering the application action that sends mail.

## Sessions

```bash
lanbox session create --ttl 900 --prefix signup --json
lanbox session get <session-id> --json
lanbox session address <session-id> --prefix reset
lanbox session delete <session-id>
```

Retain the returned session ID and delete the session during teardown.

## JSON output and exit behavior

Add `--json` for compact machine-readable output. Failures are written to standard error and return exit code `1`.

```bash
lanbox status --json
lanbox wait-otp signup@lanbox.test --timeout 10000 --json
```

## Remote or custom instances

```bash
lanbox --url http://192.168.1.20:8025/api/v1 status
lanbox address staging --domain mail.example.test
```

The domain must match the LanBox instance configuration. Keep remote instances on trusted networks with suitable external access controls.

## Destructive commands

`clear` deletes the named inbox. `clear-all` refuses to run without `--confirm`. Use these commands only when the caller owns the affected test data.

## Help

```bash
lanbox --help
lanbox --version
```
