# Public API

[Back to README](../README.md) | [TypeScript SDK](SDK.md) | [CLI](CLI.md)

This is a consumer reference for the public HTTP API. It describes user-visible requests and responses only.

Default base URL:

```text
http://127.0.0.1:8025/api/v1
```

JSON responses use `application/json`. Binary download endpoints return retained file content with response metadata where available. Errors use structured error payloads.

## Operational endpoints

### `GET /health`

Checks process liveness. Use it to determine whether the HTTP process is responding.

```bash
curl http://127.0.0.1:8025/api/v1/health
```

### `GET /ready`

Checks dependency readiness. A ready instance returns HTTP 200 with `status: "ready"`. An instance that is running but not ready returns HTTP 503 with a typed `status: "not_ready"` response.

```bash
curl http://127.0.0.1:8025/api/v1/ready
```

### `GET /info`

Returns public runtime version, bindings, limits, capture mode, and supported capabilities.

### `GET /stats`

Returns public operational statistics for the running instance.

## Message collections

### `GET /messages`

Lists captured messages. Supported filters include `search`, `to`, `from`, `subject`, `after`, and `before`. Pagination accepts `limit`, preferred `cursor`, and legacy `offset`.

```bash
curl "http://127.0.0.1:8025/api/v1/messages?to=user%40lanbox.test&limit=25"
```

Search is a case-insensitive literal match across envelope sender, recipients, and subject. It does not search message bodies, HTML, attachments, filenames, or raw email.

`nextCursor` is opaque and exclusive. Send it back unchanged. It is absent on the final page. Do not combine a cursor with a nonzero offset.

### `GET /messages/{id}`

Returns one captured message.

### `DELETE /messages/{id}`

Deletes one captured message.

### `GET /messages/{id}/raw`

Downloads retained raw email when supported by the active capture mode.

### `GET /messages/{id}/attachments/{attachmentId}`

Downloads one retained attachment when supported by the active capture mode.

### `DELETE /messages`

Clears captured messages. Use destructive collection operations only in an environment where the caller owns all affected data.

## Inboxes

### `GET /inboxes`

Lists virtual inboxes with search and pagination.

### `GET /inboxes/{address}/messages`

Lists messages delivered to one inbox. Pagination uses opaque message cursors.

### `DELETE /inboxes/{address}`

Deletes the named inbox and its captured mail.

Encode recipient addresses when placing them in URL paths.

## Deterministic testing

### `GET /testing/checkpoint`

Returns a monotonic string boundary for later queries. Capture it before the action that sends mail, then use it as `afterCursor`.

### `GET /testing/latest`

Returns the newest stored message matching the supplied recipient and optional filters.

### `GET /testing/wait`

Waits for a matching message. Common parameters include `to`, `from`, `subject`, `bodyContains`, `after`, `afterCursor`, and `timeout`.

### `GET /testing/otp` and `GET /testing/wait/otp`

Extract an OTP from existing mail or wait for an eligible message. Narrow the request with message filters and the expected OTP length when needed.

### `GET /testing/link` and `GET /testing/wait/link`

Extract a link from existing mail or wait for one. Link filters include host, path, URL content, and link text.

### `GET /testing/address/generate`

Returns a generated test address under the configured domain.

## Sessions

### `POST /testing/sessions`

Creates a temporary testing session. The request can specify a bounded TTL.

### `GET /testing/sessions/{id}`

Returns a session record.

### `POST /testing/sessions/{id}/address`

Returns a session-owned address for the supplied prefix and requested addressing mode.

### `DELETE /testing/sessions/{id}`

Deletes a session. Clean up sessions during test teardown.

## Events

### `GET /events`

Opens a server-sent event stream for supported real-time UI and consumer updates.

## Capability errors

Capture modes retain different kinds of content. When an operation is not available, LanBox returns a structured capability error such as `CAPTURE_MODE_UNSUPPORTED` rather than silently fabricating content.

Semantic extraction can return errors including `OTP_NOT_FOUND`, `OTP_AMBIGUOUS`, `LINK_NOT_FOUND`, and `LINK_AMBIGUOUS`. Treat ambiguity as a signal to narrow the request.
