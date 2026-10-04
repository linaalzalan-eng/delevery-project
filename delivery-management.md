# Delivery Management Specification

**Status:** Draft based on the stakeholder-approved intent baseline  
**Source:** [`intent.md`](./intent.md)  
**Scope:** Frontend requirements for the initial release

## 1. Purpose

Provide a responsive web frontend for small/local delivery companies to organize delivery orders and see each order's status. Dispatchers are the primary users; owners/administrators manage dispatcher accounts and driver records and view order and delivery activity.

This specification records only requirements established in the approved intent. Unresolved behavior is listed under [Open questions](#10-open-questions), not inferred.

## 2. Users and scope

### In-scope users

- **Dispatcher:** Enters orders from manual channels, manually assigns drivers, updates order statuses, searches and filters orders, and edits permitted order details.
- **Owner/administrator:** Creates and deactivates dispatcher accounts, maintains the assignable driver roster, and views order and delivery activity.

Drivers are not direct users in this release.

### Out of scope

- Driver-facing app or portal.
- Customer- or recipient-facing tracking.
- Automated dispatch or route optimization.
- Third-party integrations.

The only identified integration is the product's own backend API. Its contract is not yet known.

## 3. Functional requirements

Acceptance criteria below describe observable behavior and can be verified with automated frontend tests, using a mocked service boundary where backend behavior is not yet specified. These criteria do not define API endpoints or backend implementation.

### FR-1: Enter delivery orders

The dispatcher must be able to enter a delivery order received through a manual channel.

**Acceptance criteria**

1. An order-entry interaction is available to a dispatcher.
2. The entry interaction can capture sender name and phone number, recipient name and phone number, pickup and delivery addresses, delivery notes, package description, package quantity, optional delivery scheduling, delivery fee, and payment status.
3. The order-entry interaction supports an optional external reference.
4. On successful submission, the order data supplied by the dispatcher is represented in the resulting order without silently dropping entered values.
5. After creation, the order displays a system-generated reference, separate from the optional external reference.

**Trace:** `intent.md` §§ Project, Intended workflows, Order information and behavior (lines 3–5, 17–24, 34–47).

### FR-2: Assign a driver manually

The dispatcher must be able to assign a driver to an order by manual selection.

**Acceptance criteria**

1. The order workflow provides a manual driver-selection interaction.
2. After a successful assignment, the selected driver is shown as assigned to that order.

The list of drivers considered available and the behavior when no driver is available are not specified.

**Trace:** `intent.md` § Intended workflows (lines 17–24).

### FR-3: Update delivery status

The dispatcher must be able to update an order's status. The identified status values are **New**, **Assigned**, **Picked up**, **In transit**, **Delivered**, **Cancelled**, and **Failed/attempted**.

**Acceptance criteria**

1. The status-update interaction exposes each identified status value.
2. After a successful update, the selected status is displayed for the order.
3. A failed/attempted status cannot be successfully recorded without a reason category.
4. Failed/attempted details may be left empty.

Allowed status transitions, required status-specific data beyond the failed-attempt reason category, and repeat-attempt behavior remain unspecified.

**Trace:** `intent.md` §§ Intended workflows, Order information and behavior (lines 17–24, 49–59).

### FR-4: Find and filter orders

The dispatcher must be able to find orders by order reference or recipient and filter orders by status.

**Acceptance criteria**

1. A dispatcher can submit an order-reference query and see orders matching that reference.
2. A dispatcher can submit a recipient query and see orders matching that recipient.
3. A dispatcher can select an identified status as a filter and see only orders matching that status.

Matching rules, case sensitivity, partial matching, pagination, sorting, and the combined behavior of search and filters are unspecified.

**Trace:** `intent.md` § Intended workflows (line 24); status values in § Order information and behavior (lines 49–57).

### FR-5: Edit orders subject to pickup restrictions

Basic order details may be edited. After pickup, pickup and delivery addresses and recipient details must be locked against editing.

**Acceptance criteria**

1. Before pickup, the interface permits editing the pickup address, delivery address, and recipient details.
2. After an order is picked up, the interface prevents editing the pickup address, delivery address, and recipient details.
3. An attempted post-pickup edit of any locked field does not change that field in the displayed order.

The specific set of other editable fields and whether their editability changes with status are unspecified.

**Trace:** `intent.md` § Order information and behavior (line 59).

### FR-6: Manage dispatcher accounts

The owner/administrator must be able to create dispatcher accounts with temporary passwords and deactivate dispatcher accounts while preserving their order history.

**Acceptance criteria**

1. An owner/administrator can submit the creation of a dispatcher account with a temporary password.
2. After successful account creation, the new dispatcher is represented in the account list.
3. An owner/administrator can deactivate a dispatcher account.
4. After deactivation, the account is represented as deactivated and the dispatcher's associated order history remains available.

Password delivery, password-change behavior, account reactivation, and detailed access permissions are unspecified.

**Trace:** `intent.md` §§ Target users, Intended workflows (lines 11–14, 27–32).

### FR-7: Maintain driver records

The owner/administrator must be able to maintain the assignable driver roster.

**Acceptance criteria**

1. An owner/administrator has an interaction for maintaining the assignable driver roster.
2. Changes successfully submitted through that interaction are reflected in the displayed roster.

The required driver fields and roster lifecycle rules are not established in `intent.md`.

**Trace:** `intent.md` §§ Target users, Intended workflows (lines 11–14, 27–32).

### FR-8: View order and delivery activity

The owner/administrator must be able to view order and delivery activity.

**Acceptance criteria**

1. An owner/administrator can access an order/delivery activity view.
2. The view displays order and delivery activity data supplied to it by the frontend's data boundary.

The activity fields, aggregation, reporting, and date-range behavior are unspecified.

**Trace:** `intent.md` §§ Target users, Intended workflows (lines 11–14, 27–32).

### FR-9: Represent delivery fees and payment status

An order must represent a delivery fee in the company's fixed currency, USD, and one of the identified payment statuses: **Unpaid**, **Paid**, or **Payment on delivery**.

**Acceptance criteria**

1. The order workflow can represent a delivery fee denominated in USD.
2. The payment-status interaction exposes the three identified payment statuses.
3. The selected payment status is displayed for the order after successful submission or update.

Fee validation, precision/rounding, payment transitions, and currency configuration are unspecified.

**Trace:** `intent.md` § Order information and behavior (lines 34–47, 61–65).

## 4. Non-functional requirements and quality goals

The intent establishes the following goals, but does not provide measurable thresholds or enough detail for pass/fail automated acceptance criteria:

- Responsive desktop and mobile web experience.
- Easy-to-learn order flows, few steps for common tasks, guided help, and clear status cues.
- Basic accessibility practices; no formal accessibility conformance target is set.
- Support for current versions of major browsers; the browser/version matrix is not defined.
- Standard secure practices; no named security, privacy, or compliance standard is identified.
- Performance suitable for normal office use; numeric targets are not set.

These goals require stakeholder clarification before they can be treated as measurable acceptance criteria.

**Trace:** `intent.md` §§ Platform and technology, Quality expectations (lines 67–82).

## 5. Platform and technology constraints

- **Fixed:** React with JavaScript/JSX and Vite.
- **Preferred, not fixed:** Tailwind CSS and React Router.
- **Deferred:** State-management library, API client, and testing tools.
- Authentication is expected to be handled by the product backend.
- The product backend API is expected to be the frontend's only integration; its contract is unknown.

Do not interpret preferred or deferred choices as approved dependencies.

**Trace:** `intent.md` § Platform and technology (lines 67–74).

## 6. Constraints

- A maximum budget is expected, but its amount and coverage are not finalized.
- No launch date has been set.

**Trace:** `intent.md` § Constraints (lines 97–100).

## 7. Assumptions and boundaries

- This is a frontend specification; backend API details and authentication/session behavior remain outside the defined contract.
- Acceptance tests for API-dependent interactions can verify frontend behavior with a mocked service boundary until an API contract is agreed.
- No driver-facing or customer-facing workflow is included in this release.
- The status names, payment status names, and USD currency above are reproduced from the intent; no additional statuses or currencies are implied.

## 8. Verification approach

- Use automated component and integration tests to verify the functional acceptance criteria.
- Mock the data/service boundary for tests until the backend API contract is available.
- Do not add a testing library or select state-management/API dependencies as part of this specification; those choices are deferred in the intent.
- Do not claim measurable compliance with the non-functional goals until the relevant targets are agreed.

## 9. Traceability

| Requirement | Intent source |
|---|---|
| FR-1 Enter delivery orders | `intent.md` lines 13, 17–24, 34–47 |
| FR-2 Assign a driver manually | `intent.md` lines 13, 20–22 |
| FR-3 Update delivery status | `intent.md` lines 13, 22, 49–59 |
| FR-4 Find and filter orders | `intent.md` line 24 |
| FR-5 Edit orders subject to pickup restrictions | `intent.md` line 59 |
| FR-6 Manage dispatcher accounts | `intent.md` lines 14, 29–30 |
| FR-7 Maintain driver records | `intent.md` lines 14, 31 |
| FR-8 View order and delivery activity | `intent.md` lines 14, 32 |
| FR-9 Represent fees and payment status | `intent.md` lines 34–47, 61–65 |
| Platform and quality goals | `intent.md` lines 67–82 |
| Scope and constraints | `intent.md` lines 84–100 |

## 10. Open questions

The following intent questions remain open and must be resolved before dependent behavior can be specified more precisely:

- What are the exact screens, navigation, and layout?
- What are the role permissions and account lifecycle rules beyond dispatcher creation/deactivation?
- Which order and driver fields are required, and what validation applies?
- Which status transitions are allowed, and what data is required for each transition?
- How are multiple failed attempts and redelivery handled?
- Is a cancellation reason required?
- Which fields other than addresses and recipient details can be edited after pickup?
- What scheduling format and time windows are supported?
- How are delivery fees entered and validated, and what exactly does each payment status mean?
- What is the backend API contract, including error, loading, authentication, and session behavior?
- Which state-management, API-client, and testing tools should be selected?
- What accessibility target, browser/version matrix, and measurable performance objectives apply?
- What is the budget amount and coverage, and is there a release schedule?
- What driver fields and roster lifecycle rules are required?
- How are available drivers identified and what happens if none are available?
- What activity data must owners/administrators be able to view?
- What matching, sorting, pagination, and combined search/filter behavior is required?
