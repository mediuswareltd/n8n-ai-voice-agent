# AI Voice Agent: Lead Calling & Appointment Booking (n8n + Vapi)

**Built by Mediusware Ltd.** n8n workflow export (86 nodes). Credentials, IDs and personal data have been removed and replaced with placeholders.

## What it does

An AI voice agent that phones new leads, books an appointment inside business hours, and keeps the team and the customer informed. It fits clinics, dental offices and other appointment-based small businesses.

1. **Lead intake:** a webhook receives a new contact. The workflow normalizes name, email and phone, de-duplicates against Google Sheets, then logs the lead and creates a Trello card.
2. **AI phone call:** at the scheduled call time (a Google Calendar event), it calls the lead through the **Vapi** voice agent API.
3. **Call outcome:** it polls the Vapi call status until the call ends, reads the call analysis, and updates the sheet row and the Trello card.
4. **Booking:** it checks business opening and working hours, then creates the appointment in **Google Calendar**.
5. **Notifications:** **Gmail** sends a confirmation to the customer and an internal email to the team (confirmed or not scheduled).

## Tools & APIs connected

n8n · Vapi (REST, bearer auth) · Google Sheets · Google Calendar · Trello · Gmail · Webhook trigger · Schedule triggers

## Error handling & retries

- **No-answer / busy retry loop:** if the lead doesn't pick up or is busy, the workflow increments a call counter and schedules a new call event. It retries until the attempt limit, then notifies the team.
- **Status polling with waits:** it waits and re-checks Vapi status until the call has ended and analysis data exists, so it never acts on a call that's still in progress.
- **Fault-tolerant nodes:** 9 API and lookup nodes use *continue on error*, so one bad record or API hiccup doesn't stop the rest of the batch.
- **Global Error Trigger:** any failed execution emails the team with the workflow name, error message, execution ID and a link to the execution.
- **Duplicate protection:** leads are matched by lowercase email and phone before insert, so webhook re-deliveries don't create double calls.
- **Mock branch** for testing without placing real calls.

## Setup

1. Import `ai-voice-agent-appointment-booking.n8n.json` into n8n.
2. Add credentials for Google Sheets, Google Calendar, Gmail, Trello, and Vapi (HTTP bearer token).
3. Replace the placeholders: `YOUR_GOOGLE_SHEET_ID`, `YOUR_CALENDAR_ID`, `YOUR_VAPI_ID` (assistant and phone number IDs in the **Config** node), `YOUR_TRELLO_ID` / `YOUR_TRELLO_BOARD_ID`, and `team@example.com`.
