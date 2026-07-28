# Senate Floor Scheduler

A single-file, zero-dependency web app that models how the U.S. Senate floor actually
schedules its time — and lets you place a bill on the calendar **only in ways the rules
allow**. Pick a measure, a target date, and a set of strategy levers; the engine produces:

- **A step-by-step procedural path** to final passage, with the governing rule or precedent
  cited on every card (Rule XXII cloture clocks, motions to proceed, filling the amendment
  tree, post-cloture germaneness, reconciliation, House-message "ping-pong").
- **Override boxes** (red-striped) showing exactly how the Senate would have to override
  its normal rules to hit a date — e.g., the nuclear option's four-step precedent play, or
  why the Byrd rule blocks reconciliation for regulatory bills — with the political price tagged.
- **Consent boxes** (pink-striped) flagging every point where unanimous consent is required
  and what a single objection does to the schedule.
- **A session-day floor calendar** (the published 2026 schedule), because cloture ripening
  counts *days of session*, not calendar days.
- **Generated asks to Senate leadership** — the concrete requests to the Majority Leader's
  office, the cloakrooms, and your whip operation.
- **A whip-count meter** that treats vote math as a scheduling constraint (60 for cloture,
  51 for passage — or 51 for both under the nuclear option).
- **"Ask the Parliamentarian"** — a built-in assistant covering cloture, holds, hotlines,
  Rule XIV, vote-a-rama, and scenario commands like *"pass the CLARITY Act before the
  August recess."*

## Default scenario

H.R. 3633, the **CLARITY Act** (digital asset market structure), against an **Aug 7, 2026**
target — the last scheduled session day before the state work period. Status baked in as of
July 28, 2026: House-passed Jul 17, 2025 (294–134); reported by Senate Banking May 14, 2026
(15–9) with a substitute, so Senate passage returns the bill to the House for concurrence.
Under regular order (two cloture cycles, no time yielded back) the engine lands final
passage **Wednesday, Aug 5 — two session days to spare**.

## Run it

Open `index.html` in any browser. No build, no server, no network calls. Light and dark
themes follow the OS preference.

## What the engine encodes

| Mechanic | Rule / authority |
|---|---|
| Cloture: 16-senator petition, ripens 1 hr after convening on the 2nd session day, 60 votes, 30-hr cap | Rule XXII para. 2 |
| Debatable motion to proceed; privileged MTP for budget measures, conference reports, House messages | Rule XXII precedents; CRS R44819 |
| Post-cloture germaneness + amendment filing deadlines | Rule XXII paras. 2–3 |
| Bypassing committee to the calendar | Rule XIV paras. 2–4 |
| Reconciliation: privileged MTP, 20-hr cap, vote-a-rama, Byrd rule | Congressional Budget Act §§301–313 |
| Nuclear option: point of order → chair ruling → appeal → 51-vote new precedent | 2013/2017 precedents |
| Right of first recognition (Leader controls the floor) | Precedent since 1937 |

## Also modeled

- **Minority posture** — cooperative / standard / scorched-earth. Scorched-earth models a single
  senator burning every clock (full post-cloture time, no UC, quorum-call drag) plus leadership's
  counters (stacked cloture filings, round-the-clock sessions, dual-tracking).
- **Floor traffic** — NDAA week, appropriations/CR before the Sep 30 funding cliff (second
  shot-clock in the header), and nominations batches competing for the same session days, with a
  "who goes first" queue.
- **Nominations fast track** — non-debatable motion to executive session, 51-vote cloture
  (2013/2017 precedents), 2-hour post-cloture for district judges (2019 precedent), en-bloc UC.
- **House side** — Rules Committee concurrence or suspension (2/3) through to the President's desk.
- **Floor memo export** — one click downloads the current scenario as a Markdown memo
  (timeline + overrides + asks) ready to circulate.

## Live data & Claude-powered chat (optional backend)

`server/worker.js` is a Cloudflare Worker scaffold with three endpoints:

| Endpoint | What it does |
|---|---|
| `GET /api/status` | Live H.R. 3633 status/actions from the official [Congress.gov API](https://api.congress.gov) |
| `POST /api/chat` | Claude-powered "Ask the Parliamentarian" (Claude Opus 5, grounded in the scheduler's current state) |
| `GET /api/signals` | News-tracker signals from permitted RSS sources, with a compliant plug-in point for licensed social APIs |

Deploy with `npx wrangler deploy server/worker.js` after setting the `ANTHROPIC_API_KEY` and
`CONGRESS_API_KEY` secrets, then type `connect api https://<your-worker>.workers.dev` into the
in-app chat. Without a backend, the app runs fully offline on the built-in deterministic assistant.

**A note on X/Truth Social:** scraping either violates their terms of service, so the scaffold
doesn't. `fetchSocialSignals()` is a ready-made hook for the paid X API or a monitoring vendor
(Quorum, FiscalNote) if your team licenses one.

*A planning model, not legal advice — the presiding officer, the Parliamentarian, and 100
senators all get a say the model doesn't.*
