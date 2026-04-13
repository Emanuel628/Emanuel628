# InEx IT Support — Ladder Build Plan (Structure First)

Date: 2026-04-13 (UTC)

## Build principle

This plan follows your requested order:

1. **Build the entire app structure first (frontend and file architecture only).**
2. **No backend implementation until structure is complete and reviewed.**
3. Then climb a ladder: **JS behavior -> routes -> backend handlers -> DB/migrations -> auth/permissions -> operations/reporting**.

---

## Phase 0 — Product map and module inventory (planning only)

Goal: define exactly what the support app contains before coding behavior.

### Deliverables
- Feature inventory and module boundaries.
- Canonical case object model (field names only, no DB yet).
- Route map and screen map.
- Folder map for frontend, backend, and shared modules.

### Core feature buckets

#### Foundation
- Tenant-aware support
- Customer/account linkage
- Case types (bug / support / feature request)
- Status + priority + severity + business impact
- Global search
- Attachments/evidence

#### Investigation
- Auth/session troubleshooting views
- Export/receipt failure investigation views
- Event/system context panel
- Case timeline
- Safe admin actions panel
- Runbooks/playbooks library

#### Operations
- Internal notes vs customer-visible updates
- Audit trail
- Escalation workflow
- SLA response/resolution targets
- Duplicate detection + incident grouping
- Role-based permissions

#### Product intelligence
- Reporting dashboards
- Trend analysis
- Bug/request separation analytics
- Incident linkage + status broadcast awareness

---

## Phase 1 — App skeleton only (no real logic)

Goal: scaffold the full app structure so every major page/section exists.

### Build now
- App shell (layout, nav, topbar, tenant switch shell)
- Route skeletons for all planned pages
- Empty page components with placeholder sections
- Shared UI primitives (cards, table shell, filters shell, timeline shell, attachment shell)
- State/store scaffolding with mock adapters only
- API client interfaces as stubs (no live calls)

### Suggested page skeleton set
- Case inbox/list
- Case detail
- Attachments/evidence tab
- Timeline tab
- System context tab
- Customer communication tab
- Safe admin actions tab
- Runbooks tab
- Incidents/duplicate grouping view
- Reporting/trends view
- Admin permissions/roles view

### Exit criteria
- You can click through the complete app information architecture.
- Every core feature bucket is visibly represented in navigation.
- No backend dependency yet.

---

## Phase 2 — Frontend behavior with mock data only

Goal: make the shell interactive before touching backend.

### Build now
- Case list filtering/sorting/search UX (mocked)
- Case detail data composition (mocked sections)
- Timeline rendering (status changes, notes, assignments, system events)
- Attachment uploads to local/mock provider
- Duplicate detection UI flow (suggestions + link to parent incident)
- Severity + impact editing UI
- SLA badges (due/overdue state) from mock timestamps
- Role-gated UI visibility (mock roles)
- Safe admin action controls (disabled/mock execution)

### Exit criteria
- End-to-end demo works entirely with mock data.
- Team can validate UX and IA without backend churn.

---

## Phase 3 — JavaScript domain layer hardening (still no real backend)

Goal: stabilize contracts so backend can be added without UI rewrites.

### Build now
- Shared JS/TS schemas for:
  - Case
  - Attachment
  - Timeline event
  - Incident group
  - SLA
  - Admin action request/result
- Validation and normalization utilities
- Frontend repository/service interfaces (contract-first)
- Error-state model (network, permission, validation, rate-limit)

### Exit criteria
- Mock data and components consume the same strict contract shapes.
- Replacing mock adapters with live adapters will be mostly plug-and-play.

---

## Phase 4 — Route contracts and API surface definition

Goal: define backend routes before implementation.

### Build now
- OpenAPI/spec or route contract docs for first ladder endpoints:
  - Cases: list, details, create, update status, assign
  - Attachments: create/upload, list, delete/soft-delete
  - Timeline/events: list by case
  - Incidents: create parent incident, link/unlink duplicate cases
  - Search: global query endpoint
  - Safe admin actions: explicit action endpoints (permission-gated)
- Request/response examples and error contracts.

### Exit criteria
- Frontend contracts and backend contracts are aligned in writing.
- No endpoint implementation yet.

---

## Phase 5 — Backend implementation (thin vertical slices)

Goal: implement one route family at a time.

### Ladder order
1. Read-only cases (`GET /cases`, `GET /cases/:id`)
2. Case mutation (`POST /cases`, `PATCH /cases/:id/status`, assignment)
3. Attachments/evidence routes
4. Timeline + event context aggregation routes
5. Search across IDs/reference fields
6. Duplicate detection + incident grouping routes
7. Safe admin action execution routes

### Exit criteria
- Each ladder rung ships with handler + tests before moving to next rung.

---

## Phase 6 — Database + migrations (added incrementally per ladder rung)

Goal: introduce persistence only as each backend slice is stabilized.

### Build now
- Initial migrations for cases/users/roles/status catalogs
- Follow-up migrations for:
  - attachments
  - timeline events
  - incidents + case_incident_links
  - SLA tracking fields
  - admin action audit logs
  - tags/categorization
- Index strategy for search keys (email, business name, case ID, export ID, receipt ID, Stripe refs)

### Exit criteria
- Every implemented route has corresponding migration and rollback path.

---

## Phase 7 — Auth, permissions, and data safety

Goal: lock down access before scale.

### Build now
- Auth/session handling
- Role-based access controls (support agent, support lead, admin, read-only)
- Permission matrix for sensitive actions/data (billing, impersonation tools, tenant metadata)
- Sensitive-content flags and redaction helpers
- Explicit audit logging for every safe admin action

### Exit criteria
- Unauthorized access paths are blocked and tested.
- Sensitive operations are explicit, logged, and reviewable.

---

## Phase 8 — Operations workflow layer

Goal: make support repeatable, not ad hoc.

### Build now
- Internal vs customer-visible note channels
- Response templates and communication history tracking
- SLA timers and escalation queue
- Runbooks/playbooks linked to case types
- Incident status linkage for broadcast scenarios

### Exit criteria
- Support can handle recurring issues consistently and traceably.

---

## Phase 9 — Reporting and product intelligence

Goal: convert support activity into decision-grade signals.

### Build now
- KPI dashboards:
  - first response time
  - resolution time
  - top issue categories
  - export failure rate
  - auth friction patterns
  - billing issue frequency
- Repeat-user/repeat-tenant problem detection
- Bug vs feature request vs support trend separation

### Exit criteria
- Weekly review can prioritize roadmap based on measurable support pain.

---

## Highest-priority capabilities to include early in structure

If you want the strongest outcomes, make sure these are in the **initial skeleton** from Phase 1:

1. Attachments/evidence
2. System event context
3. Safe admin actions
4. Global search
5. Severity + business impact
6. Duplicate detection + incident grouping

These are the capabilities that make support effective in real operations, not just organized.
