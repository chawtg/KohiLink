# KohiLink non-functional requirements (v1.0)

Targets are measurable. Values marked "proposed" are my starting points and can be changed. Because KohiLink is a personal project with a running cost of NZ$0, free-tier limits are design constraints, and several targets are relaxed compared with a commercial service.

Requirement IDs are stable and are not reused, so the numbering has intentional gaps. Offline driver mode is out of scope for this version; the API will still make driver status updates idempotent and timestamped so it can be added cheaply later.

## Performance and scalability
| ID | Requirement | Target |
|---|---|---|
| NFR-1 | API responsiveness (excluding route suggestion) | 95% of requests within 1 s once the app is warm, at 20 concurrent users (proposed) |
| NFR-2 | Page load | Customer booking list (50 bookings) within 2 s; driver route page within 3 s on simulated 4G |
| NFR-3 | Route suggestion | 50 pickups and 5 drivers within 30 s (US-017); relax to 60 s if free compute cannot meet it, and record the change |
| NFR-4 | Demo scale (proposed) | 500 customers, 10 drivers, 100 pickups per day, with NFR-1 and NFR-2 still met on seeded data |

## Reliability and data integrity
| ID | Requirement | Target |
|---|---|---|
| NFR-5 | Availability | Best effort; no uptime guarantee on free hosting. First request after an idle period completes within 60 s (proposed) |
| NFR-6 | Backup and recovery | Automated database export at least weekly, last 4 kept; a restore tested once before release; maximum data loss 7 days (demo data) |
| NFR-7 | Consistency | A status change and its audit entry are saved together or not at all; a pickup is on at most one active route; simultaneous edits are detected |
| NFR-8 | External service failure | If email, geocoding or routing fails, bookings and driver updates still work; the failed call is retried up to 3 times and logged; routing falls back to straight-line distances and flags routes as approximate |

## Security
| ID | Requirement | Target |
|---|---|---|
| NFR-10 | Sign-in and transport | HTTPS only; passwords stored with a modern adaptive hash and never logged; sessions expire after 8 hours |
| NFR-11 | Authorisation | Checked on the server for every request, with an automated test for each role boundary (US-003) |
| NFR-12 | Common vulnerabilities | OWASP Top 10 risks addressed; input validated on the server; no unresolved high or critical dependency findings at release |
| NFR-13 | Secrets | Keys and connection strings kept out of the repo; secret scanning runs in CI |
| NFR-14 | Abuse protection | Rate limit on sign-in and registration, for example 10 requests per minute per IP (works with the lock-out in US-002) |
| NFR-15 | Proof uploads | JPEG or PNG only, up to 5 MB, compressed on upload to about 500 KB (proposed); stored privately and served only to authorised users (US-024) |

## Privacy
| ID | Requirement | Target |
|---|---|---|
| NFR-16 | Data minimisation | Drivers see only the details of their assigned stops; design considers the NZ Privacy Act 2020 principles |
| NFR-17 | Retention (proposed) | Pickup personal details anonymised 12 months after completion; proof photos deleted after 90 days; customers can request account deletion |
| NFR-18 | Demo and logs | No real personal data in the repo, screenshots or demo (synthetic seed data only); logs never contain passwords or tokens |

## Usability and accessibility
| ID | Requirement | Target |
|---|---|---|
| NFR-19 | Mobile-first | All driver screens usable at 360 px width; touch targets at least 44 px |
| NFR-20 | Accessibility | Core flows meet WCAG 2.2 AA; text contrast at least 4.5:1; Lighthouse accessibility score of 90 or more |
| NFR-21 | Ease of use | A first-time customer completes a booking in 3 minutes or less without help (checked with 3 testers); errors say what went wrong and how to fix it |

## Compatibility and localisation
| ID | Requirement | Target |
|---|---|---|
| NFR-22 | Browsers and devices | Latest 2 versions of Chrome, Edge, Firefox and Safari; Chrome on Android and Safari on iOS; driver app installable as a PWA |
| NFR-23 | Time zones | Times stored in UTC and shown in NZ time including daylight saving; collection hours and slots judged in NZ local time; tested around a daylight-saving change |
| NFR-24 | Local formats | NZ address and phone formats; metric units (kg, cm, km); Auckland service area held in configuration |

## Maintainability and operations
| ID | Requirement | Target |
|---|---|---|
| NFR-25 | Automated tests | At least 80% line coverage of business logic (capacity, status transitions, validation, 48-hour and slot rules); integration tests on the main flows; CI must pass before merge |
| NFR-26 | Continuous integration | Build, lint, tests and dependency scan on every pull request; main branch always deployable |
| NFR-27 | Configurable rules | Collection hours, slots, lead time, service area, size presets, item and weight limits and lock-out values held in configuration, not hard-coded |
| NFR-28 | Setup and docs | A new developer runs the system locally in 15 minutes with one command; API documented with OpenAPI |
| NFR-29 | Observability | Structured logs with a request ID; health-check endpoint answers within 1 s |
| NFR-30 | Deployment | Automated deploy from main; database changes through versioned migrations; rollback to the previous version within 15 minutes |

## Cost
| ID | Requirement | Target |
|---|---|---|
| NFR-31 | Running cost | NZ$0 per month: only free tiers or open-source tools; no service may upgrade to a paid plan automatically |
| NFR-32 | Free-tier quotas | Usage of email, geocoding and routing services stays under provider limits and is logged; when a limit is reached, the system degrades gracefully (queue emails, use straight-line distances) instead of failing |

## Architecturally significant requirements
These six groups drive the design and each deserve an ADR:
1. Consistency (NFR-7)
2. Route suggestion performance (NFR-3)
3. Resilience to outside services (NFR-8)
4. Security and privacy (NFR-10 to 12, 15 to 17)
5. Availability against cost (NFR-5, NFR-31, NFR-32)
6. Changeability (NFR-25, NFR-27, NFR-28)

## How these are verified
Load tests (NFR-1 to 4), Lighthouse and manual checks (NFR-19 to 21), CI checks (NFR-12, 13, 25, 26), automated API tests (NFR-11), a restore drill (NFR-6), and a rollback drill (NFR-30).
