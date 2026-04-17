# InEx Ledger 2.0 — Diagnostics Section Spec

Date: 2026-04-17 (UTC)

## Goal

Add a **Diagnostics** area to Settings that improves transparency and supportability without exposing sensitive internals or confusing primary users.

## Product decision

- Do **not** place raw developer metadata in core user settings categories.
- Add a dedicated **Diagnostics** section, with controlled visibility and safe redaction.
- Prefer "Diagnostics" as the label instead of "Developer Tools".

## Information architecture

Recommended Settings navigation:

1. Profile / Business
2. Preferences
3. Security
4. Billing
5. Help & Support
6. Diagnostics

### Visibility policy

Use one of the following patterns (in priority order):

1. **Progressive disclosure (recommended)**
   - Show Diagnostics only when the user taps **"Need technical help?"** in Help & Support.
2. **Role/flag-gated**
   - Show Diagnostics for support role, admin role, or enabled troubleshooting flag.
3. **Always visible but concise**
   - Keep section visible but with user-safe wording and no raw internals.

## Diagnostics content model

### Section A — App Info

- App version (semantic version)
- Build number
- Commit SHA (short)
- Environment label (`Production`, `Staging`, etc.)
- Release timestamp

### Section B — Session & Account Status

- Auth/session status (Active/Expired)
- Last token refresh time
- Account/plan entitlement summary
- Feature flag evaluation status (names only where safe)

### Section C — Export & Sync Health

- Export subsystem status (Healthy/Degraded/Down)
- Last successful export timestamp
- Queue depth bucket (e.g., `0`, `1-10`, `10+`) if applicable
- Last error code + human-readable message

### Section D — Client Technical Context

- Browser + version
- OS/device class
- Time zone and locale
- Network status (online/offline)

### Section E — Recent Errors

- Last 5 client-visible errors
- For each item: timestamp, code, user-safe message, correlation/request ID
- No stack traces by default in user-facing view

### Section F — Actions

- **Copy support info** button (single structured payload)
- **Download diagnostics** (JSON/TXT) if needed for support workflows
- **Open support ticket** shortcut with prefilled metadata

## Security and privacy requirements

Never expose in Diagnostics:

- Access/refresh tokens
- API keys or secrets
- Raw PII beyond what is already visible in account profile
- Internal hostnames, private service URLs, or sensitive backend topology
- Full server stack traces or database errors

Required protections:

- Redact any token-like string by default
- Truncate IDs where full values are unnecessary
- Sanitize all error objects before rendering
- Gate any "advanced" payload behind explicit user action

## UX copy recommendations

Use user-safe labels:

- "Diagnostics"
- "Support Info"
- "Technical Details"
- "Troubleshooting"

Avoid internal-sounding labels in UI chrome:

- "Dev Tools"
- "Debug Console"
- "Internal Status"

## Implementation notes (engineering)

### Data contract for "Copy support info"

Suggested payload (redacted):

```json
{
  "app": {
    "version": "2.0.0",
    "build": "2026.04.17.1",
    "commit": "abc1234",
    "environment": "production"
  },
  "session": {
    "status": "active",
    "lastRefresh": "2026-04-17T13:30:00Z",
    "plan": "pro"
  },
  "export": {
    "status": "healthy",
    "lastSuccess": "2026-04-17T13:22:10Z",
    "lastError": {
      "code": "NONE",
      "message": null
    }
  },
  "client": {
    "browser": "Chrome 135",
    "os": "macOS",
    "timezone": "America/New_York",
    "locale": "en-US",
    "online": true
  },
  "recentErrors": [
    {
      "time": "2026-04-17T12:57:02Z",
      "code": "EXPORT_TIMEOUT",
      "message": "Export took too long. Please retry.",
      "correlationId": "req_7fa..."
    }
  ]
}
```

### API boundary

- Prefer deriving most diagnostics from already-available client/runtime data.
- For backend-driven health, expose only **safe summaries** via a dedicated diagnostics endpoint.
- Enforce a stable schema to prevent breaking support tooling.

### Observability linkage

- Include correlation ID in surfaced errors.
- Ensure copied diagnostics payload includes correlation IDs for last error events.
- Keep ID format consistent with backend logs and support search tools.

## Rollout plan

1. Ship read-only Diagnostics with App Info + Session + Client Context.
2. Add Export Health and Recent Errors once stable error normalization is in place.
3. Add support workflow actions (copy/download/prefill) after privacy review.

## Acceptance criteria

- Diagnostics is discoverable from Help & Support without cluttering core settings.
- Users can copy a sanitized support payload in one click.
- No secrets/tokens/private config are exposed in any Diagnostics view.
- Support can use exposed codes/correlation IDs to locate issues quickly.
- Terminology is user-safe and consistent across web/mobile surfaces.

