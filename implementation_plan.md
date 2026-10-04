# Implementation Plan

**Status:** Working implementation plan based on [`delivery-management.md`](./delivery-management.md) and [`intent.md`](./intent.md)

## 1. Goal

Deliver a mock-backed, responsive frontend for the first release of a delivery order management system. One developer is responsible for requirements alignment, implementation, testing, and documentation. The plan prioritizes completing and validating the dispatcher order journey end to end before moving to administration and activity views.

The mock-backed frontend is a demonstrable frontend prototype, not a production-ready delivery management system. Real authentication, authorization, persistence, and enforcement of business rules require a backend.

## 2. Scope and constraints

### In scope

- Dispatcher order entry, manual driver assignment, status updates, search/filtering, and permitted editing.
- Owner/administrator dispatcher-account management, driver-roster maintenance, and order/delivery activity view.
- Login, logout, mock authentication, and role-based frontend route guards for the two application roles.
- A service boundary with mock data and a conditional backend adapter.
- Responsive desktop/mobile presentation and testable loading, error, and empty states.

### Out of scope

- Driver-facing app or portal.
- Customer- or recipient-facing tracking.
- Maps, route optimization, automated dispatch, and electronic payment processing.
- Third-party integrations.
- New order statuses, permissions, cancellation rules, retry/redelivery rules, or assignment rules not established by the requirements.

### Constraints and confirmed choices

- **Confirmed:** React, Vite, and JavaScript/JSX.
- **Preferred in the requirements, not yet installed/approved as dependencies:** Tailwind CSS and React Router.
- **Deferred in the requirements:** State-management, API-client, and testing-library selections.
- The product backend is the only identified integration; its API contract and authentication/session behavior are unknown.
- The product backend must enforce authentication, authorization, and business rules. Frontend route guards and mock authentication are not security controls.
- Budget amount, launch date, measurable performance targets, and formal accessibility target are not set.

## 3. Implementation principles

- Work sequentially as one developer: keep one implementation task active at a time, finish and verify it before starting the next.
- Prioritize the complete dispatcher order path: create, find, inspect, assign, update status, and edit where permitted.
- Implement only behavior supported by confirmed requirements. Block dependent behavior on an explicit decision rather than guessing.
- Keep presentation, feature logic, and service/data access separate; use small shared UI components where they prevent real duplication.
- Start with the mock service boundary and representative non-sensitive fixtures so features and tests do not depend on an unavailable API.
- Keep temporary/mock authentication distinct from production authentication. Never put passwords in display data, browser storage, or activity logs.
- Verify each phase as it is implemented; perform a cross-flow review and full agreed checks before calling the mock-backed frontend complete.
- Keep commits small and focused; do not introduce dependencies until the proposed choices are accepted.

## 4. Recommended project structure

Adapt the existing Vite project incrementally; do not reorganize unrelated starter files solely to match this example.

- `src/`
  - `app/` — app shell, route definitions, layouts, and session context
  - `features/`
    - `auth/` — login, logout, mock authentication, and role guards
    - `orders/` — order list, create, detail, edit, and status/assignment interactions
    - `dispatchers/` — dispatcher account administration
    - `drivers/` — driver roster administration
    - `activity/` — administrator activity view
  - `components/` — shared navigation, form controls, status presentation, and state views
  - `services/` — shared service interface and Mock/API adapters
  - `mocks/` — non-sensitive fixtures and mock service behavior
  - `utils/` — focused formatting and validation helpers
  - `styles/` — global styles or shared tokens if needed

Keep each feature's page and feature-specific components near that feature. Extract shared hooks only when they improve clarity or reuse.

## 5. Functional delivery plan

Effort estimates are preliminary hands-on hours for one developer, including that phase's implementation and focused verification. They are not a release-date commitment. Phase 6 is conditional and excluded from the mock-backed frontend estimate.

### Phase 1: Requirements alignment, app shell, and shared layout

**Deliverables**

- Confirm the initial screen/navigation proposal and mark unresolved behavior in the open-question register.
- Responsive app shell with role-aware navigation and shared page/layout primitives.
- Proposed route map and placeholder views, without a separate Dashboard:
  - `/login`
  - `/orders` — Dispatcher start page/order list
  - `/orders/new`
  - `/orders/:orderId`
  - `/orders/:orderId/edit`
  - `/admin/dispatchers`
  - `/admin/drivers`
  - `/admin/activity` — Admin start page/activity view
  - `/unauthorized`
  - Not Found route
- `:orderId` denotes a dynamic identifier; it replaces the blank path segments in shorthand forms such as `/orders/` and `/orders//edit`.

**Acceptance focus**

- Each proposed route renders the intended placeholder or shared shell.
- Dispatcher start navigation points to `/orders`; Admin start navigation points to `/admin/activity`.
- Responsive layout and basic keyboard/label checks are made during implementation.
- No standalone dashboard or unapproved business workflow is added.

**Dependencies**

- Confirmed roles and functional scope from the requirements.
- Screen details remain a planning decision; use this route map as a proposal until confirmed.

**Preliminary estimate:** 4–6 hours.

### Phase 2: Data layer, mock authentication, and access control

**Deliverables**

- Define frontend models for `Order`, `Driver`, `Dispatcher`, and `Activity`, keeping unresolved fields/rules explicitly undecided.
- Define one asynchronous service interface for orders, drivers, dispatchers, and activity; implement a Mock Adapter behind it. Keep an API Adapter boundary compatible with the same interface for Phase 6.
- Seed non-sensitive mock fixtures; model order internal ID, `systemReference`, and optional `externalReference` as separate values.
- Add mock Login/Logout and session context using React local state and Context for the current mock session/role.
- Add role-based route guards, redirect unauthenticated users to `/login`, unauthorized Dispatcher visits to `/unauthorized`, and a Not Found route.
- Add reusable Loading, Error, and Empty states and service failure paths.
- Do not store passwords in fixture/display data, browser storage, or activity logs. Mock credentials/session behavior must not be represented as production security.

**Acceptance focus**

- Mock login/logout updates the in-memory session and navigation state.
- A Dispatcher attempting to open an Admin route directly by URL is denied in the frontend and directed to `/unauthorized`.
- Unauthenticated access to protected routes returns to `/login`; unknown paths render Not Found.
- Service calls are asynchronous and pages depend on the service interface, not directly on mock fixtures/adapters.
- Loading, error, and empty results have distinct, observable UI states.
- Automated tests cover session and guard behavior. Real authentication and authorization remain backend responsibilities.

**Dependencies**

- Route proposal from Phase 1.
- Confirm which role can access each view and the mock sign-in demonstration behavior before building the corresponding interactions.

**Preliminary estimate:** 6–10 hours.

### Phase 3: Order management core

**Deliverables**

- Order list, create, detail, and edit screens wired to the service interface.
- Form controls for all fields identified in the requirements: sender/recipient names and phone numbers, pickup/delivery addresses, notes, package description and quantity, optional schedule, delivery fee/USD, payment status, and optional external reference.
- Generated `systemReference` displayed separately from internal order ID and optional `externalReference`.
- Manual driver selection, order status update, and search/filter controls.
- Pickup restriction enforced in both UI and Mock Adapter/service behavior for pickup/delivery addresses and recipient details.
- Only implement the status-transition policy after it is confirmed; do not infer a transition graph from the status names.

**Acceptance focus**

- A successfully created order appears in the order list and detail view with entered values preserved and a generated system reference.
- Saving is blocked when stakeholder-confirmed required fields are missing. Required-field definitions must be decided first; do not invent which fields are mandatory.
- When a save fails, entered values remain available for correction/retry and an error is shown.
- Search by reference or recipient and status filtering can be used together; combined behavior and matching details must be agreed before final acceptance expectations are fixed.
- A successful driver assignment is reflected on the order.
- A status transition rejected by the confirmed transition policy does not update the displayed or service-backed order.
- Failed/attempted cannot be saved without a reason category; optional details may be blank.
- After pickup, editing locked addresses or recipient details is prevented in the UI and rejected by the service layer; attempted updates do not alter stored mock order data.
- Tests use the service boundary and cover success, validation, failure preservation, combined search/filter, assignment, approved/rejected transitions, failed reason, and pickup locks.

**Dependencies**

- Confirm required fields and validation, search matching/combination behavior, status transitions, assignment availability/no-driver behavior, schedule rules, fee validation, and any post-pickup edit rules beyond confirmed locks.
- Phases 1 and 2.

**Preliminary estimate:** 18–26 hours.

### Phase 4: Dispatcher account and driver administration

**Deliverables**

- Admin dispatcher list and create/deactivate interactions, including temporary-password entry as required.
- Driver roster maintenance using only confirmed driver fields and lifecycle actions.
- Retain deactivated dispatcher records and their order-history associations.
- Remove drivers from the active roster without deleting the driver record or historical order associations.
- Never retain or log temporary passwords in display data, browser storage, or activity logs.

**Acceptance focus**

- Successful dispatcher creation is reflected in the list; deactivation is reflected as inactive and does not remove associated order history.
- Successful driver roster changes appear in the roster.
- Removing a driver from active selection does not erase historical orders or their driver association.
- Tests cover success/failure and preservation of historical relationships.

**Dependencies**

- Confirm dispatcher permissions/account lifecycle, required driver fields, and permitted roster lifecycle actions.
- Phase 2 service models and Phase 3 order association.

**Preliminary estimate:** 8–12 hours.

### Phase 5: Activity and reporting view

**Deliverables**

- Admin activity view at `/admin/activity`, configured as the Admin's start page.
- Render Activity data supplied by the service boundary, with loading, error, and empty states.
- Limit display and summaries to stakeholder-confirmed activity fields and reporting behavior.

**Acceptance focus**

- Admin can access activity view; Dispatcher cannot access it by direct URL.
- The page renders supplied activity data and handles loading/error/empty responses.
- Deactivation and driver-roster removal do not destroy order history or historical relationships.
- Tests verify data rendering and role access without inventing aggregations or date rules.

**Dependencies**

- Confirm activity fields, filtering, date range, and aggregation expectations.
- Phases 2 and 4; order events/data from Phase 3.

**Preliminary estimate:** 6–10 hours.

### Phase 6: Backend integration — conditional

**Deliverables**

- Implement the API Adapter behind the existing service interface only after the backend contract is available.
- Map request/response data and backend validation, loading, error, and authentication/session behavior to the frontend.
- Keep mock mode available for isolated UI tests and demonstrations where appropriate.
- Document integration configuration and any API-contract assumptions.

**Acceptance focus**

- The agreed backend contract supports the same user-visible flows as the mock adapter.
- Authentication and authorization are enforced by the backend; frontend guards remain UX controls only.
- API and mock adapter behavior is checked against the same service-level expectations.
- No successful-looking fallback hides API failures.

**Dependencies**

- Conditional: approved API contract, reachable backend environment, and agreed authentication/session behavior.
- Mock-backed Phases 2–5.

**Preliminary estimate:** 8–16 hours after the contract is available; revisit based on API scope.

### Phase 7: Validation and quality hardening

**Deliverables**

- Complete the agreed component/integration test set; propose Vitest and React Testing Library, subject to approval.
- Run the repository's existing lint and production build checks.
- Review desktop/mobile behavior, basic accessibility, error recovery, and role boundaries.
- Perform end-to-end manual review of the Dispatcher order journey and Admin flows.
- Update implementation documentation and record known limitations.

**Acceptance focus**

- All confirmed functional acceptance criteria pass against the mock service; if integrated, the agreed API scenarios also pass.
- No known regression in route access, order data preservation, history links, or pickup locks.
- Existing `npm run lint` and `npm run build` pass. New test commands apply only after proposed test dependencies are approved and added.
- Non-functional goals are described honestly; no unapproved formal conformance claim is made.

**Dependencies**

- Phases 1–5; Phase 6 only for an integrated release.
- Stakeholder approval of the testing stack and any measurable quality targets.

**Preliminary estimate:** 8–12 hours.

## 6. Proposed user flows

### Dispatcher

1. Open `/login` and enter mock credentials for the demo; real credentials/authentication are not part of mock mode.
2. After successful mock sign-in, land on `/orders`.
3. Create an order at `/orders/new`; on success, view it by its internal route identifier and generated system reference.
4. Search by system/order reference or recipient and combine search with a status filter once matching behavior is confirmed.
5. Manually assign a driver and update status according to the confirmed transition policy.
6. Edit allowed details; after pickup, locked address and recipient fields remain unchanged.
7. Log out and return to `/login`.

### Owner/administrator

1. Sign in using mock mode and land on `/admin/activity`.
2. Review activity supplied by the service.
3. Manage dispatcher accounts at `/admin/dispatchers`.
4. Maintain the active driver roster at `/admin/drivers`, retaining records needed by order history.
5. Log out.

These flows describe proposed navigation and frontend behavior. They do not define backend endpoints or unresolved business rules.

## 7. Technical approach

### Baseline and proposed tools

- **Confirmed baseline:** React + Vite + JavaScript/JSX.
- **Proposed, consistent with documented preferences:** Tailwind CSS for styling and React Router for route handling. Both require dependency approval before installation.
- **Proposed:** React local state for feature-local state and React Context for the in-memory session; do not add Redux unless later requirements demonstrate a concrete need.
- **Proposed:** native `fetch` for API calls behind the service/API adapter. The API contract and error mapping remain unknown.
- **Proposed:** Vitest + React Testing Library for automated UI tests, subject to approval of the testing approach and dependencies.

The proposed tools are not recorded as previously approved or installed.

### Models, services, and persistence

- Define `Order`, `Driver`, `Dispatcher`, and `Activity` around fields established in the requirements; mark other fields as pending confirmation.
- Keep internal order ID, system-generated reference, and optional external reference separate.
- Use asynchronous services with a single interface and replaceable Mock Adapter/API Adapter.
- Keep page components independent from adapter-specific data structures.
- Provide explicit loading, error, and empty states.
- Mock frontend data is not durable production storage. Never place passwords in display data, browser storage, or activity records. Production credentials/session handling must follow the backend contract.

### Authentication and authorization

- Mock Login/Logout and Context-based session state exist only to demonstrate role-aware frontend navigation.
- The frontend can prevent Dispatcher navigation into Admin screens, including direct URL attempts, but this is not a security boundary.
- Production backend must authenticate users, enforce role permissions, validate business rules, and protect data regardless of frontend route behavior.

### Order rules and validation

- Implement only established statuses: New, Assigned, Picked up, In transit, Delivered, Cancelled, and Failed/attempted.
- Do not create allowed-transition rules until approved. Once defined, enforce and test the same policy in the UI and service boundary; the backend must also enforce it in an integrated release.
- Require a failed-attempt reason category; failed-attempt details remain optional.
- Lock pickup/delivery addresses and recipient details after pickup both in the UI and service boundary.
- Confirm required order fields before applying blocking required-field validation.

## 8. Risks and mitigation

### Individual workload and scope growth

**Risk:** One developer owns requirements, implementation, tests, and documentation; parallel work or expanding scope can delay the core flow.

**Mitigation:** Deliver sequentially, keep one active implementation task, prioritize the end-to-end order journey, use preliminary hour estimates for planning, and defer out-of-scope work.

### Ambiguous order rules

**Risk:** Required fields, transitions, cancellation, retries, scheduling, and assignment behavior are incomplete.

**Mitigation:** Maintain decisions as open questions, block affected acceptance details until confirmed, and avoid invented rules/statuses.

### Backend delay or API-contract mismatch

**Risk:** The frontend may be demonstrable before a backend exists, and the later contract may differ from mock assumptions.

**Mitigation:** Build and test against a service interface with a Mock Adapter first; make backend integration conditional, map errors explicitly, and verify the API adapter against the agreed contract.

### Historical relationships lost

**Risk:** Deactivating a dispatcher or removing a driver from active selection could orphan historical order information.

**Mitigation:** Preserve dispatcher and driver records/associations needed by order history; deactivate or remove from the active roster rather than deleting historical identity; add tests for retained history.

### Mock authentication mistaken for real security

**Risk:** A demo login or frontend route guard could be treated as production access control.

**Mitigation:** Label mock mode as demonstration-only; require backend authentication, authorization, and business-rule enforcement for any integrated/production use.

### Dependency and target uncertainty

**Risk:** Preferred tools and quality goals have not all been approved or measured.

**Mitigation:** Present Tailwind, React Router, fetch, and Vitest/React Testing Library as proposals; confirm before adding packages or claiming measurable non-functional compliance.

## 9. Open questions that must be resolved before final implementation detail

### Before the related feature

- **Before Phase 1 navigation is finalized:** Confirm exact screens, layout, and navigation; the paths in this plan are proposals.
- **Before Phase 2 authentication/access behavior:** Confirm access assigned to each role, mock sign-in demonstration behavior, and any account lifecycle/permissions beyond dispatcher creation and deactivation.
- **Before Phase 3 order forms:** Confirm required order fields and validation, schedule format/time windows, delivery-fee validation, and payment-status meaning/transitions.
- **Before Phase 3 search/filter acceptance is finalized:** Confirm matching rules, case/partial matching, and combined search/filter behavior; pagination and sorting if needed.
- **Before Phase 3 assignment/status features:** Confirm driver availability/no-driver behavior, allowed order-status transitions, status-specific data, and required failed-attempt reason categories. Confirm repeated attempts/redelivery behavior and whether cancellation requires a reason.
- **Before Phase 3 edit behavior is finalized:** Confirm which fields other than addresses and recipient details remain editable after pickup.
- **Before Phase 4:** Confirm driver fields and roster lifecycle actions, plus dispatcher account lifecycle and temporary-password delivery/change behavior.
- **Before Phase 5:** Confirm activity fields, date-range behavior, and aggregation/reporting requirements.

### Before real backend integration

- Backend API contract, environment, and error/loading response behavior.
- Production authentication/session lifecycle and backend role enforcement.
- Data contract for order IDs/references and retained historical dispatcher/driver relationships.
- Backend enforcement for required fields, status transitions, pickup locks, and other confirmed business rules.

### Planning decisions requiring confirmation

- Approve or revise proposed screens/routes and role landing pages.
- Approve proposed Tailwind CSS and React Router dependencies.
- Approve React local state + session Context as sufficient and native `fetch` as the API approach.
- Approve Vitest + React Testing Library and the testing scope before adding dependencies.
- Confirm accessibility target, supported browser/version matrix, measurable performance goals, budget amount/coverage, and release timing.
- Confirm whether the initial deliverable is mock-backed only or includes the conditional integrated release.

### Possible post-MVP deferrals

- Driver-facing app/portal and customer-facing tracking.
- Maps, route optimization, automated dispatch, and third-party integrations.
- Advanced reporting beyond confirmed order/delivery activity.
- Any additional workflow not established in the current MVP requirements.

## 10. Delivery milestones

Milestones follow the seven phases and are executed sequentially by the single developer.

1. **Requirements and shell:** Confirm immediate UI decisions, implement route/layout proposal, and verify responsive shell.
2. **Mock foundation and access:** Complete models, shared asynchronous services, fixtures, mock session, guards, and tested loading/error/empty states.
3. **End-to-end order workflow:** Complete the Dispatcher order path before starting secondary administration; verify create-to-detail, search/filter, assignment, status, and edit restrictions.
4. **Administration:** Complete dispatcher and driver roster operations with history-preservation checks.
5. **Activity:** Complete the Admin activity view using confirmed data fields and reporting behavior.
6. **Conditional integration:** Connect the API Adapter only when the backend contract/environment is ready; otherwise explicitly deliver mock-backed frontend scope.
7. **Release review:** Run the agreed automated checks, lint/build, responsive/accessibility review, and manual role/flow review; update docs and known limitations.

### Simple Git and task workflow

- Keep `main` as the working, verified version.
- Create short-lived branches for individual features/fixes; merge them back after self-review and focused tests.
- Use a Pull Request only if required by the course/project workflow or useful for documenting the change; no `develop` branch or team approval process is needed.
- Maintain a simple To Do / In Progress / Done list. Keep exactly one implementation task in In Progress; mark it Done only after its focused verification passes.

## 11. Exit criteria for the initial release

### Ready to begin feature implementation

- The route/screen proposal is approved or remaining UI decisions are explicitly accepted as provisional.
- Decisions needed immediately for the next feature are resolved; later questions remain tracked until their dependent phase.
- The proposed dependencies and mock-auth demonstration behavior are approved before installation/implementation.
- The feature backlog can be executed sequentially by one developer with one active task.

### Ready to deliver the mock-backed frontend

- Phases 1–5 and Phase 7 are complete against the confirmed requirements.
- The end-to-end Dispatcher order journey and Admin flows pass agreed tests and manual review using the Mock Adapter.
- Confirmed rules—including field validation, status policy, role access, pickup locks, and historical relationship preservation—are tested; any unresolved rules are clearly excluded rather than guessed.
- Lint and production build pass; known limitations and mock-mode behavior are documented.
- This is a **frontend demonstration/prototype**, not a system ready for real operational use: it does not provide production authentication, backend-enforced authorization, durable persistence, or a verified real API.

### Ready to deliver an integrated release

- The backend API contract and target environment are available and approved.
- Phase 6 maps the agreed contract through the API Adapter, including explicit error and session behavior.
- Backend enforces authentication, authorization, data validation, status/business rules, and access to protected data.
- Integrated acceptance scenarios and the agreed frontend checks pass, including preservation of historical relationships.
- Deployment/release requirements and any remaining security, accessibility, browser, performance, budget, and schedule decisions are resolved or formally accepted.
