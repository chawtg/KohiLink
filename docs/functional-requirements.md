# KohiLink functional requirements (v1.0)

KohiLink is a pickup booking and route planning platform for a logistics company. Customers book pickups, coordinators plan routes, and drivers collect items using a mobile-friendly web app (PWA).

## Scope and assumptions
- Parcels and small freight only.
- One logistics company; no multi-company support.
- Service area is Auckland only.
- Route planning is batch planning with coordinator approval; there is no live re-optimisation.
- Drivers use a mobile-friendly web app (PWA), not a native app.
- Route suggestion is a core feature.

## Business rules
| Rule | Value | Held in |
|---|---|---|
| Collection hours | 09:00 to 17:00, NZ local time | Configuration |
| Customer time slots | Four fixed 2-hour slots: 09:00 to 11:00, 11:00 to 13:00, 13:00 to 15:00, 15:00 to 17:00 | Configuration |
| Slot meaning | Estimated arrival window; a planning target, not a guarantee | Business rule |
| Booking lead time | At least 48 hours, counted back from 09:00 on the pickup date | Configuration |
| Customer cancel or reschedule cut-off | Same 48-hour rule; after that only a coordinator can cancel | Configuration |
| Service area | Auckland only (list of suburbs or a boundary) | Configuration |
| Items per booking | 1 to 20 | Configuration |
| Weight per item | 0.1 to 30 kg | Configuration |
| Size presets (max length x width x height, cm) | Small 30 x 20 x 10; Medium 40 x 30 x 20; Large 60 x 40 x 30; Extra large 80 x 50 x 40 (proposed) | Configuration |
| Time spent at each stop | Not modelled (product owner decision); arrival times use travel time only | Business rule |
| Route start | One depot address; no return to the depot is modelled | Configuration |
| Account lock-out | 5 failed sign-ins locks the account for 15 minutes (proposed) | Configuration |
| ETA window | Planned arrival plus or minus 30 minutes, clipped to the booked slot (proposed) | Configuration |

## Requirements
Requirement IDs are stable and are not reused, so the numbering has intentional gaps.

### Accounts and access
- **FR-1:** Users sign in with email and password and have one role: Customer, Driver or Coordinator.
- **FR-2:** Each role sees only its own functions, enforced on the server.
- **FR-3:** Customers self-register. Coordinators create driver accounts.

### Customer
- **FR-4:** Create a pickup booking with an Auckland address, access notes, a pickup date at least 48 hours ahead, one of the four 2-hour time slots, a contact number, and 1 to 20 items, each with a size preset and a weight of up to 30 kg.
- **FR-5:** View their bookings and current status.
- **FR-6:** Cancel a booking until 48 hours before 09:00 on the pickup date. After that, only a coordinator can cancel.
- **FR-7:** Reschedule a booking to another date and time slot under the same 48-hour rule. A failed pickup can be rescheduled to a date at least 48 hours ahead.
- **FR-8:** See an ETA window once the pickup is assigned.
- **FR-9:** Receive email notifications on status changes.

### Coordinator
- **FR-10:** View incoming pickup requests, filtered by status, date and area, and accept them.
- **FR-11:** Manage drivers and vehicles: maximum load weight, cargo volume, availability, and each driver's daily shift (within collection hours) and vehicle.
- **FR-12:** Manually assign pickups to a driver and order the stops.
- **FR-13:** Generate suggested routes for a day's pickups, respecting vehicle weight and volume capacity, customer time slots and driver shift hours.
- **FR-14:** Review, edit and approve suggested routes before they go to drivers.
- **FR-15:** Handle same-day changes: add, remove, cancel or reassign pickups, including cancelling inside the customer's 48-hour cut-off.
- **FR-16:** View a dashboard of today's routes and pickups, refreshed regularly.

### Driver
- **FR-18:** See today's approved route as an ordered stop list with address, access notes, time slot, items and their sizes and weights.
- **FR-19:** Open a stop in an external navigation app.
- **FR-20:** Update stop status: en route, arrived, collected, failed.
- **FR-21:** Record a reason code when a pickup fails.
- **FR-22:** Capture proof of pickup (photo or signature).

### System
- **FR-25:** Enforce the status lifecycle: Requested → Scheduled → Assigned → En route → Collected or Failed, plus Cancelled and Rescheduled. A coordinator accepts Requested or Rescheduled bookings into Scheduled. Invalid transitions are rejected.
- **FR-26:** Keep an audit history of every status change (who, when, from, to).
- **FR-27:** Validate all inputs (30 kg limit, size presets, 1 to 20 items, Auckland service area, 48-hour lead time, valid slot) and return clear error messages.
- **FR-28:** Convert addresses to map coordinates (geocoding).
- **FR-29:** Calculate travel distance and time between stops.

## Out of scope
Payments and invoicing, international shipping documents, multi-company accounts, live GPS tracking, real-time re-optimisation, native mobile apps, SMS notifications, offline driver mode (deferred), time spent at each stop.

## Open items
- Slots are a target, not a guarantee. Confirm if the business ever needs them as a promise.
- Proposed values to confirm: preset dimensions, ETA window width, lock-out limits.
