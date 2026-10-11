<div align="center">

<img src="app/static/logo.svg" width="72" alt="Zenith Desk logo">

# Zenith Desk

**One workplace for contact-center operators — calls, tickets, Telegram, WhatsApp and e-mail in a single window.**
Built on top of **Zammad** and **FreePBX / Asterisk**, open-source only, no paid CRM or VoIP modules.

**English** · [Русский](README.ru.md)

![version](https://img.shields.io/badge/version-3.9-1fb87a)
![python](https://img.shields.io/badge/python-3.12-3d7fe0)
![fastapi](https://img.shields.io/badge/FastAPI-async-0f141c)
![postgres](https://img.shields.io/badge/PostgreSQL-16-3d7fe0)
![checks](https://img.shields.io/badge/release%20check-250%2B%20checks%20passing-1fb87a)
![languages](https://img.shields.io/badge/UI-English%20%7C%20Russian-5d6675)

<img src="docs/screenshots/manager-overview.jpg" alt="Zenith Desk — manager overview" width="100%">

</div>

---

## Contents

- [Why Zenith Desk](#why-zenith-desk)
- [Features](#features)
- [Screenshots](#screenshots)
- [Architecture](#architecture)
- [Quick start (demo)](#quick-start-demo)
- [Production install](#production-install)
- [Integrations](#integrations)
- [Reliability and security](#reliability-and-security)
- [Testing](#testing)
- [Project layout](#project-layout)
- [Documentation](#documentation)
- [Version history](#version-history)

---

## Why Zenith Desk

Operators usually juggle a desk phone, Zammad, messengers and a mail client — and Asterisk keeps sending them calls
while they are busy with a ticket. Zenith Desk puts everything in one window and lets the **server decide who can
take calls**:

| | |
|---|---|
| **Start** a ticket (blocking) | the operator is **paused in every managed Asterisk queue** — only after Asterisk confirms it |
| **Background task** | the ticket stays with the operator, calls keep coming |
| **Complete** | the ticket gets its final state in Zammad, calls come back — unless a break or another blocking task remains |
| **Ready** | refused while a blocking task exists; a manual break is never removed by closing a ticket |

Operator states: `READY` · `BUSY_TASK` · `BREAK` · `OFFLINE` · `WRAP_UP`. Priority: offline/break › blocking tasks › ready.
Ticket state and operator state are separate (`work_mode = blocking | background`).

---

## Features

### Operator
- **Dashboard** — today's numbers, active and background tasks, new requests from every channel with *Start* /
  *Background task*, tickets assigned to me, notifications, recent calls. Blocks can be rearranged, resized and hidden;
  the layout follows the user to any computer.
- **Incoming call** — the caller is found in Zammad by the normalized number, the customer card opens with recent
  tickets; unknown caller → *Create customer*. *Create ticket from call* attaches number, time, extension, queue,
  call ID and recording. Optionally a ticket is created automatically for every answered call.
- **Zenith Phone** — a WebRTC softphone with MicroSIP functionality (JsSIP → FreePBX): up to 3 lines, hold, blind and
  attended transfer, 3-way conference, DTMF, call recording, voicemail, auto-answer, *Do not disturb* (paused in all
  queues), device selection. A slim call strip under the top bar shows the call on every page.
- **Tickets inside Zenith Desk** — Mine / Unassigned / All open / Closed, search and filters, full conversation
  (e-mail, calls, Telegram, WhatsApp, notes) with attachments, reply or internal note, state / priority / group,
  hand-over to a colleague. Everything is written to Zammad **on behalf of the operator**; Zammad group rights apply.
- **Customers** — Zammad customers and users, search by name / phone / e-mail / company, customer card with tickets.
- **Team chat** — Telegram-style messenger for staff: read receipts, typing, reactions, edit / delete, desktop push
  notifications; messages encrypted at rest and visible only to the two participants.
- **Telegram & WhatsApp** — **one company bot and one company number** (no personal numbers): Open / Mine /
  Unassigned / Closed, *Take* and *Transfer*, photos / files / voice, ✓ sent · ✓✓ delivered · read, internal notes,
  quick replies, *Call*, *Start*. Every conversation is mirrored into a Zammad ticket. WhatsApp 24-hour window and
  approved templates are handled.
- **Mail** — the employee types **only e-mail and password**; IMAP or POP3 + SMTP are set once by the administrator.
  Folders, search, safe HTML (sanitized, remote images on request), attachments, reply / reply all / forward,
  drafts, *Create ticket* from an e-mail, new mail instantly via IMAP IDLE.
- **Inactivity pause**, dark / light theme, animations, English / Russian, works from a 360 px phone to a 4K monitor.

### Manager (level *Manager*)
- **Manager panel** — every operator's state **and the reason** (which ticket, blocking or background), queue pause
  and Asterisk sync status; filter by level 1 / 2 / 3.
- *Assign ticket* by number (optionally start it), **override** with a mandatory reason (audited), cancel a session,
  **reconcile** with Asterisk and Zammad, audit log, 24-hour report.
- **Overview** — today / yesterday / 7 / 30 days: incoming, answered, missed, **service level** gauge, answer rate,
  average speed of answer and talk time, live queue, calls by hour, staffing vs workload (Erlang), leaders,
  *missed callers not called back*, per-operator table with occupancy, *not picked up* and engagement index.
  Every formula is explained on the page.
- Plays **call recordings** of the whole team (operators only their own; every play is audited).

### Administrator
- **Users** — own logins (no Zammad password needed), level = rights, link to a Zammad user and an extension,
  SIP password for the browser phone, password reset, lock-out after failed sign-ins.
- **Integrations** — tabs Zammad · Telephony · Telegram · WhatsApp · Mail · Sync, each with **Test connection**
  against the real service; webhook URLs and verify tokens ready to copy. Secrets are stored encrypted, changes apply
  without restart.
- **Settings**, **ticket types with required fields** (validated before *Start*), **system status**,
  data-retention periods.

### Windows desktop app
`ZenithDesk-Setup-3.9.exe` — a classic setup wizard (C# / .NET Framework 4.8 + Edge WebView2): system check with
download of missing Microsoft components, server address and port with a **mandatory connection test**, desktop
shortcut, autostart, uninstall from *Installed apps*, silent mode `/S /SERVER= /PORT=`. `ZenithDesk.exe` keeps running
in the notification area and brings the window back on an incoming call. See [windows/README.md](windows/README.md).

---

## Screenshots

<table>
<tr>
<td width="50%"><img src="docs/screenshots/operator-dashboard.jpg" alt="Operator dashboard"><br><b>Operator dashboard</b> — tasks, new requests from every channel, notifications</td>
<td width="50%"><img src="docs/screenshots/incoming-call.jpg" alt="Incoming call"><br><b>Incoming call</b> — customer found in Zammad, call strip with Answer / Decline</td>
</tr>
<tr>
<td><img src="docs/screenshots/zenith-phone.jpg" alt="Zenith Phone"><br><b>Zenith Phone</b> — WebRTC softphone during a call</td>
<td><img src="docs/screenshots/ticket.jpg" alt="Ticket"><br><b>Ticket page</b> — conversation, properties, Start / Background</td>
</tr>
<tr>
<td><img src="docs/screenshots/telegram.jpg" alt="Telegram"><br><b>Telegram</b> — company bot, photo from a customer, Zammad ticket</td>
<td><img src="docs/screenshots/whatsapp.jpg" alt="WhatsApp"><br><b>WhatsApp</b> — company number, 24-hour window, delivered ✓✓</td>
</tr>
<tr>
<td><img src="docs/screenshots/mail.jpg" alt="Mail"><br><b>Mail</b> — folders, reading, reply, create ticket from e-mail</td>
<td><img src="docs/screenshots/mail-sign-in.jpg" alt="Mail sign-in"><br><b>Mail sign-in</b> — only e-mail and password</td>
</tr>
<tr>
<td><img src="docs/screenshots/customers.jpg" alt="Customers"><br><b>Customers</b> — Zammad customers with their tickets</td>
<td><img src="docs/screenshots/team-chat.jpg" alt="Team chat"><br><b>Team chat</b> — encrypted staff messenger</td>
</tr>
<tr>
<td><img src="docs/screenshots/manager-panel.jpg" alt="Manager panel"><br><b>Manager panel</b> — states, reasons, reconcile, override</td>
<td><img src="docs/screenshots/overview-team.jpg" alt="Team overview"><br><b>Team overview</b> — occupancy, not picked up, engagement</td>
</tr>
<tr>
<td><img src="docs/screenshots/admin-users.jpg" alt="Users"><br><b>Administration</b> — users, levels, Zammad link, extension</td>
<td><img src="docs/screenshots/admin-whatsapp.jpg" alt="WhatsApp integration"><br><b>Integrations</b> — WhatsApp number with connection test</td>
</tr>
<tr>
<td><img src="docs/screenshots/admin-mail.jpg" alt="Mail server settings"><br><b>Mail server</b> — IMAP / POP3, SMTP, ports, encryption</td>
<td><img src="docs/screenshots/admin-sync.jpg" alt="Sync panel"><br><b>Sync</b> — real time + fallback checks for every system</td>
</tr>
<tr>
<td><img src="docs/screenshots/my-settings.jpg" alt="My settings"><br><b>My settings</b> — theme, layouts, sounds, phone style</td>
<td><img src="docs/screenshots/admin-system.jpg" alt="System status"><br><b>System status</b> — integrations, errors, sessions</td>
</tr>
<tr>
<td><img src="docs/screenshots/windows-setup.png" alt="Windows setup"><br><b>Windows setup wizard</b> — mandatory connection test</td>
<td align="center"><img src="docs/screenshots/mobile.jpg" alt="Mobile" width="45%"><br><b>Mobile</b> — the same app on a phone</td>
</tr>
</table>

---

## Architecture

```mermaid
flowchart TB
    UI["Zenith Desk workplace<br/>browser · Windows app · WebRTC phone"]
    S["Zenith Desk server<br/>state service · blocking/background rules<br/>sync hub · audit · retries · REST API"]
    DB[("PostgreSQL<br/>states · sessions · audit")]
    Z["Zammad<br/>REST · webhooks"]
    A["FreePBX / Asterisk<br/>AMI · WebRTC (PJSIP)"]
    T["Telegram<br/>Bot API"]
    W["WhatsApp<br/>Cloud API (Meta)"]
    M["Mail server<br/>IMAP · POP3 · SMTP"]
    UI -- "HTTPS + SSE (live events)" --> S
    S --> DB
    S --> Z & A & T & W & M
```

- **Server:** Python 3.12, FastAPI, async SQLAlchemy 2, PostgreSQL 16, Alembic migrations, one process
  (SSE broker and per-agent locks live in memory), Docker Compose. Live events reach the browser in 50–80 ms.
- **Web UI:** Vue 3 without a build step, IBM Plex fonts, JsSIP 3.13 for the phone — all served locally.
- **Desktop:** C# / .NET Framework 4.8 WinForms + Microsoft Edge WebView2.
- Zammad and FreePBX are **never modified** — only their official APIs (REST, webhooks, AMI) are used.

**A call, step by step:** customer calls queue 400 → AMI event → the operator's screen shows the customer card from
Zammad → the operator answers in Zenith Phone and creates a ticket → *Start* (blocking) assigns the ticket in Zammad
and pauses the operator in all queues after Asterisk confirms → the manager sees “busy with ticket #…” → *Complete*
closes the ticket and unpauses, unless another reason remains.

---

## Quick start (demo)

The demo replaces Zammad and Asterisk with in-memory fakes and starts Telegram / WhatsApp API simulators, so every
screen works without real systems.

```bash
pip install -r requirements-dev.txt
python -m scripts.demo                       # http://localhost:8000
UI_LANGUAGE=ru python -m scripts.demo        # Russian interface
```

| User | Password | Role |
|---|---|---|
| `anna`, `boris` | `pw` | operators |
| `boss` | `pw` | manager |
| `admin` | `admin` | administrator |

```bash
curl -X POST "localhost:8000/demo/call?ext=101&number=89123456789"            # incoming call
curl -X POST "localhost:8000/demo/telegram/message?name=Maria&text=Hello"     # message to the company bot
curl -X POST "localhost:8000/demo/whatsapp/message?name=Aram&text=Hello"      # message to the company number
```

The demo mailbox works when a local mail server is running: `sh tests/realstack/mailserver.sh start`
(Dovecot + Postfix; users `anna@company.test` / `anna-mail-1`, `boris@company.test` / `boris-mail-2`).

---

## Production install

A technician installs the server once; everything else is done by the administrator in the web panel.

```bash
cp .env.example .env            # set POSTGRES_PASSWORD and JWT_SECRET (openssl rand -hex 32) — never change JWT_SECRET later
docker compose up -d --build
docker compose logs app | grep -A3 "FIRST ADMINISTRATOR"     # first administrator's login and password
```

Put it behind a TLS reverse proxy (nginx example with `proxy_buffering off` for the event stream in
[docs/INSTALL.md](docs/INSTALL.md)), then sign in as administrator:

1. **Integrations → Zammad** — URL, service token, webhook token; add the shown webhook URL and a trigger in Zammad.
2. **Integrations → Telephony** — AMI host / user / password (`read = call,agent,dialplan`, `write = agent`), managed
   queues, Zenith Phone WebSocket address.
3. **Users** — create employees, link each to a Zammad user and an extension.
4. Optional: **Telegram**, **WhatsApp**, **Mail**, **ticket types**, **retention**.

Forgot the admin password: `docker compose exec app python -m app.cli reset-password admin`.
Interface language: *Settings → General → Interface language* (`en` / `ru`) or `UI_LANGUAGE` in `.env`.

---

## Integrations

| System | How | Real time | Fallback check (every 5 min, configurable) |
|---|---|---|---|
| **Zammad** | REST API on behalf of the operator, signed webhooks | webhook | finds tickets changed without a webhook, reconciles sessions |
| **FreePBX / Asterisk** | AMI (pause / unpause with result check), PJSIP WebSocket for the phone | AMI events | compares every operator's pause with the real queue state |
| **Telegram** | company bot, Bot API | long polling (no public address needed) or webhook with secret header | re-registers the webhook, fetches missed updates |
| **WhatsApp** | WhatsApp Business Platform Cloud API, signed webhooks | webhook from Meta | checks number and token, re-sends the outbox, re-copies to Zammad |
| **Mail** | IMAP / POP3 + SMTP with the employee's own login | IMAP IDLE | checks for new mail, downloads new POP3 mail |

*Integrations → Sync* shows for every system: real time on / off, last event, last and next check, recovered items,
**retry-queue length**, last error and a *Check now* button. `GET /api/health` returns a short summary for monitoring.

> **Gmail:** IMAP / SMTP accept an **App Password** (requires 2-Step Verification), not the normal Google password.

---

## Reliability and security

- After a restart the service loads states from the database and **reconciles with Asterisk**; a pause is never
  removed while the queue state is unknown. AMI commands are confirmed by the response and the event, retried with
  back-off; *Ready* is never shown as confirmed while the queue state is unknown.
- Idempotent webhooks and commands (`X-Zammad-Delivery`, `Idempotency-Key`) — no duplicate sessions.
- HTTPS + HSTS, strict Content-Security-Policy, no framing; passwords hashed; tokens, SIP and mail passwords, chat and
  messenger messages **encrypted at rest**; webhook signatures verified (Zammad HMAC, Telegram secret, Meta SHA-256).
- Call recordings are played through Zenith Desk with rights check and audit; configurable retention for audit,
  calls and messages.
- Backup: `pg_dump` of the service database plus the `.env` file — see [docs/OPERATIONS.md](docs/OPERATIONS.md).

---

## Testing

```bash
pip install -r requirements-dev.txt && playwright install chromium
pytest                                                                   # SQLite
TEST_DATABASE_URL=postgresql+asyncpg://aws:aws@localhost/aws_test pytest   # PostgreSQL
python -m scripts.release_check                                           # everything, before every release
```

| Check | Count | Against |
|---|---|---|
| Server tests | 129 | SQLite and PostgreSQL: availability rules, all acceptance criteria, API |
| UI end-to-end | 72 | Chromium: operator, manager, administrator, mobile |
| WebRTC phone | 12 | SIP-over-WebSocket server: register, calls, hold, DTMF, transfer |
| **Real Asterisk 20.6** | 8 | AMI with minimal rights, pause / DND, Asterisk restart, queue call answered in the browser, recording |
| **Real mail server** | 6 | Dovecot (IMAP, POP3, IDLE) + Postfix (SMTP) |
| Windows app | 28 | setup wizard, silent install / removal, connection test, program window (Mono) |
| Migrations | 4 | upgrade, model = schema, downgrade, upgrade again |

The real-system runs are scripted: `tests/realstack/asterisk.sh start && python tests/realstack/asterisk_e2e.py`.

---

## Project layout

```
app/                 FastAPI service
  api/               REST endpoints (OpenAPI at /docs, exported to docs/openapi.json)
  messengers/        Telegram and WhatsApp connectors
  mail/              IMAP / POP3 / SMTP
  static/            web UI (Vue 3, no build), Zenith Phone, fonts
  sync_hub.py        real-time + fallback synchronisation of every integration
migrations/          Alembic migrations
scripts/             demo server, API simulators, release check
tests/               pytest suites, UI and phone end-to-end tests, realstack/ (real Asterisk and mail)
windows/             C# setup wizard and desktop client, tests
docs/                install, operations, acceptance, ТЗ compliance, OpenAPI, screenshots
```

---

## Documentation

| Document | |
|---|---|
| [docs/INSTALL.md](docs/INSTALL.md) | installation: FreePBX / AMI, Zammad, TLS proxy, Telegram, WhatsApp, mail, recordings |
| [docs/OPERATIONS.md](docs/OPERATIONS.md) | updates, backup and restore, monitoring, behaviour during failures, limitations |
| [docs/ACCEPTANCE.md](docs/ACCEPTANCE.md) | acceptance test protocol |
| [docs/TZ_COMPLIANCE.md](docs/TZ_COMPLIANCE.md) | requirement-by-requirement compliance with the specification |
| [docs/openapi.json](docs/openapi.json) | OpenAPI of the service API (107 methods), interactive at `/docs` |
| [windows/README.md](windows/README.md) | Windows setup and client |
| [LICENSES.md](LICENSES.md) | third-party components and their licenses |
| [CHANGELOG.md](CHANGELOG.md) | every version in detail |

Most operational documents are written in Russian.

---

## Version history

| Version | Highlights |
|---|---|
| 1.0–1.4 | state service, Zammad + AMI, operator and supervisor UI, tests, Docker Compose, EN / RU |
| 2.0–2.3 | administration panel, own logins, dashboard, notifications, levels decide rights |
| 2.4–2.9 | *Command Center* design, manager overview, live mode (50–80 ms), inactivity pause, responsive |
| 3.0–3.4 | staff messenger, tickets and customers inside, Zammad API contract check, new name *Zenith Desk* |
| 3.5–3.7 | Zenith Phone (WebRTC, MicroSIP features), call strip, server-side Do not disturb |
| 3.8 | Windows setup wizard and desktop client |
| **3.9** | Telegram, WhatsApp, mail, background sync of every system, protected recordings, retention, real Asterisk 20 tests |

---

<sub>Third-party components are open source; see <a href="LICENSES.md">LICENSES.md</a>. Zammad and FreePBX/Asterisk are used only through their public APIs.</sub>
