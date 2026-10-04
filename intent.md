# Product Intent

## Project

A responsive frontend for a SaaS Delivery Order Management System for small/local delivery companies.

## Product purpose

Help delivery companies organize and track delivery orders, with clearer visibility into each order's status.

## Target users

- **Dispatcher:** Primary day-to-day user. Enters orders received through phone, email, or similar manual channels; manually assigns drivers; and updates order statuses.
- **Owner/administrator:** Manages dispatcher accounts and driver records, and views order and delivery activity.
- **Drivers:** Not direct users of the system in the current MVP scope.

## Intended workflows

### Dispatcher

1. Enter an order received through a manual channel.
2. Assign an available driver manually.
3. Update the order status as delivery progresses.
4. Search for orders by order reference or recipient and filter by status.
5. Edit permitted order details, subject to the post-pickup restrictions below.

### Owner/administrator

- Create dispatcher accounts with temporary passwords.
- Deactivate dispatcher accounts while preserving their order history.
- Maintain the assignable driver roster.
- View order and delivery activity.

## Order information and behavior

The order information identified so far includes:

- A system-generated order reference and an optional external reference.
- Sender and recipient names and phone numbers.
- Pickup and delivery addresses.
- Delivery notes.
- Package description and quantity.
- Optional delivery scheduling.
- Assigned driver.
- Delivery fee and payment status.

The company uses one fixed currency for delivery fees. The currency selected so far is USD.

Identified order statuses:

- New
- Assigned
- Picked up
- In transit
- Delivered
- Cancelled
- Failed/attempted

A failed delivery attempt has a reason category and optional details. Basic order details may be edited, but pickup and delivery addresses and recipient details are locked after pickup.

Identified payment statuses:

- Unpaid
- Paid
- Payment on delivery

## Platform and technology

- React with JavaScript/JSX and Vite are fixed project choices.
- The frontend should be responsive for desktop and mobile web.
- Tailwind CSS and React Router are preferred.
- State-management, API-client, and testing-tool selections are deferred until requirements clarify their needs.
- Backend communication is expected to use the product's own API. Its contract is not yet known.
- Authentication is expected to be handled by the product backend.

## Quality expectations

- Prioritize ease of learning through straightforward order flows, few steps for common tasks, guided help, and clear status cues.
- Follow basic accessibility practices; no formal accessibility target has been selected.
- Support current versions of major browsers.
- Use standard secure practices. No named security, privacy, or compliance standard has been identified.
- Provide performance suitable for normal office use. Numeric performance targets have not been set.

## MVP scope

The current MVP boundary is the dispatcher and owner/administrator workflows described above.

Explicitly out of scope for the first release:

- Driver-facing app or portal.
- Customer- or recipient-facing tracking.
- Automated dispatch or route optimization.
- Third-party integrations.

The only identified integration is the product's own backend API.

## Constraints

- A maximum budget is expected, but its amount and coverage are not finalized.
- No launch date has been set.

## Open questions

The following details have not yet been established and must not be treated as decided requirements:

- Exact screens, navigation, and layout.
- User roles, permissions, and account lifecycle beyond the administrator actions listed above.
- Required-field rules and validation for orders and driver records.
- Allowed order-status transitions and whether status changes need additional data.
- Handling of multiple failed delivery attempts or redelivery.
- Whether cancellation requires a reason; this question was not answered.
- Detailed behavior for order edits after pickup, beyond the stated address and recipient-detail locks.
- Scheduling format and supported time windows.
- Delivery-fee entry rules and the precise meaning of each payment status.
- Backend API contract, error handling, loading behavior, and authentication/session details.
- Final state-management, API-client, and testing-tool choices.
- Formal accessibility target and measurable performance objectives.
- Budget amount, budget coverage, and any release schedule.
