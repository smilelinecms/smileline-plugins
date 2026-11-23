---
name: smileline
description: Use when working in a Smileline dental practice through the Smileline connection — today's calls and tasks, finding patients and enquiries, moving an enquiry along, logging a call outcome, adding tasks or notes, reading and sending messages, appointments, and practice reports.
---

# Working in Smileline

Smileline is a dental practice CRM. This plugin connects to it through one
server, `smileline`, for practices in Europe and the United States alike: at
sign-in the person chooses where their practice's account is. If it is not
connected, ask them to run `/mcp` and sign in to `smileline`.

Every call acts as the person who approved the connection, with their role's
permissions, inside the one practice they chose.

## How operations work

The server exposes three tools:

1. `search_operations` finds an operation and returns its id
   (`METHOD /path`) and input schema.
2. `read` runs a `GET` operation, and `write` runs anything else.
3. Path parameters go in `params`, query parameters in `query`, and the JSON
   body in `body`. Dates and times are ISO 8601. Lists page with their own
   `limit`/`cursor` or `offset` parameters.

Check an operation's schema with `search_operations` before its first use.
Never guess an id: look it up. `GET /bootstrap` returns the practice's
members, pipelines and stages, contact methods, lost and cancellation reasons,
locations, practitioners, appointment types and message templates in one call.

Writes carry what the schema asks for: `expectedVersion` (the version you
last read), `requestId` or `clientRef` (a fresh UUID that makes a retry
safe). A `409` names its reason in `code`. Only `VERSION_CONFLICT` means
the record changed: read it again, check the change still makes sense, and
retry once with the new version. Any other `409` (a clashing booking, a
practice-system site, a lead someone else has open) needs the person to
decide.

Before a write that contacts a patient (a message) or changes a booking,
show the person what will be sent or booked and wait for their yes.

## Common jobs

| Job | Operations |
| --- | --- |
| Today's work | `GET /today`: the practice's queue in buckets (`callNow`, `overdue`, `today`, `upcoming`). For one person: their id from `members` in `GET /bootstrap`, then `GET /tasks` with `assigneeId` and `due=today` (or `overdue`). |
| Find a patient or enquiry | `GET /patients` with `q` (name, email, phone, patient number or date of birth); `GET /journeys` with `q` for enquiries. |
| Read a record | `GET /patients/:id` with `embed=timeline,journeys,touches`; more history from `GET /patients/:id/timeline`. |
| Move an enquiry | `POST /journeys/:id/move` with `expectedVersion` and `toStageId` (stage ids from `GET /bootstrap`). Moving to the won stage wins it. `POST /journeys/:id/reopen` reopens a closed one. |
| Log a call outcome | `POST /tasks/:id/outcome`, or `POST /journeys/:id/outcome` when the enquiry has no open task. See the rules below. |
| Add a task or note | `POST /tasks`; `POST /patients/:id/notes`. |
| Messages | `GET /conversations` (`unread`, `assignee=me`) and `GET /inbox/counts`; reply with `POST /conversations/:id/messages`; start one with `POST /conversations`. |
| Appointments | `GET /appointments` with `from` and `to`; book with `POST /appointments`. Finding free time is done in the Smileline app. |
| Reports | `GET /page/dashboard`; one report with `GET /reports/:report` (for example `speed-to-lead`, `funnel`, `lead-sources`, `attendance`). |

### Call outcomes

Set `contacted` to say whether the person was reached.

- Reached (`contacted: true`, with `contactMethodId` from `GET /bootstrap`):
  `considering`, `callback_later`, `booked`, `not_proceeding`,
  `going_ahead`.
- Not reached (`contacted: false`): `no_answer`, `message_sent`,
  `not_proceeding`, and the consultation-check outcomes `attended`,
  `no_show`, `going_ahead`, `declined`, `cancelled`.
- `booked` takes one of `consultationAt`, `booking` or `appointmentId`.
- A `lostReasonId` is required with `not_proceeding` after a conversation and
  with `declined` or `cancelled`. Unreached `not_proceeding` sends none.
- After a conversation, `followUp` (`channel`, `dueAt`) sets the next
  contact. It is not sent with `going_ahead`, with a losing outcome, or with
  `booked` when the consultation is given (the attendance check comes next).
  After `no_answer`, `message_sent` or `no_show`, the practice's call-back
  rhythm sets it.
- `POST /tasks/:id/outcome/undo` undoes an outcome within 60 seconds and
  never cancels a booking. `declined`, `cancelled` and an answer that also
  marked the visit in the diary cannot be undone; reopen a closed enquiry with
  `POST /journeys/:id/reopen` instead.

An enquiry is lost through a `not_proceeding` outcome, not by moving it.

## What stays in the Smileline app

Card payments and refunds, billing purchases, paid SMS registration and
numbers, ad spend, AI image and video generation, and AI-call setup are done
in the Smileline app. Sending a patient a deposit link and pausing a campaign
are available here. Clinical records and the rest of the practice-management
workspace, calls and call recordings, and the person's own account stay in
the app too.
