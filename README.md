<div align="center">

# QUBRIX Control Desk

**Real-time hackathon operations checklist for organisers, built for phones.**

The Turing Club · AGM Rural College of Engineering and Technology, Varur, Hubballi
12-Hour Internal Hackathon · 17–18 November 2026

![Single file](https://img.shields.io/badge/app-single%20HTML%20file-1D4ED8)
![No build](https://img.shields.io/badge/build-none-111827)
![Backend](https://img.shields.io/badge/backend-Supabase-065F46)
![Mobile first](https://img.shields.io/badge/UX-mobile%20first-92400E)

</div>

---

## What it is

QUBRIX Control Desk turns the printed *Hackathon Operations Master Checklist & Responsibility Pack* (32 pages) into a live web app. Every organiser opens the same link on their phone, and every tick, status, note and budget change syncs to everyone else in real time. Every change is recorded in an audit log.

There is no build step and no framework. The whole app is one `index.html` plus a one-time `setup.sql`.

## Features

| Area | What you get |
|---|---|
| **Access** | One shared control-desk passcode. Organisers enter name and role, with no registration. |
| **Checklists** | Master Register (15 workstreams), all 22 stages, and the Page 32 additional checks. Each row has a checkbox, owner, **P / IP / B / D** status and an issue note. |
| **Stage headers** | Editable Owner, Backup, Deadline and Checked by for every stage. |
| **Stage notes** | A notepad under every section with auto-save, last editor and time, plus timestamped quick notes. |
| **Blockers** | Anything marked **B** appears in a Critical Blockers banner for the Lead Organiser. |
| **Safety quick-dial** | Stage 21 cards for campus security, faculty-in-charge, first aid and the nearest medical facility, with tap-to-call. |
| **Budget (Appendix A)** | Plan min/max pre-filled. Enter paid teams, then approved and actual amounts per line. Income, expenditure and net balance update live. |
| **Run sheet (Appendix B)** | A timeline for 17–18 Nov with **Stamp start / Stamp end** buttons, owner, status and deviations. |
| **Audit log** | A slide-over drawer with live updates and filters. It records who changed what, the old and new value, and when. |
| **Export** | CSV backup of statuses, notes, budget and audit logs, plus a print view. |
| **Mobile UX** | Tap status buttons, 48px targets, bottom Prev/Next bar, named stage navigation, collapsible filters and safe-area support. |

## Quick start

```text
1. Create a free Supabase project
2. Run setup.sql in the Supabase SQL Editor
3. Paste your Project URL + anon key into index.html
4. Host index.html (Netlify / GitHub Pages / Vercel)
5. Share the link and passcode with organisers
```

### 1. Create the database

1. Sign up at [supabase.com](https://supabase.com) and click **New project**.
2. Name it `qubrix`, generate a database password and choose the **Mumbai** region.
3. Open **SQL Editor → New query**, paste the contents of `setup.sql` and click **Run**.
4. Confirm that `records` and `audit_logs` appear under **Table Editor**.

### 2. Configure the app

Get your keys from **Project Settings → API Keys**. Use the **anon** or **publishable** key and never the `service_role` key. Then edit the config line near the top of the script in `index.html`:

```js
const CFG = {
  SUPABASE_URL: 'https://YOUR-PROJECT.supabase.co',
  SUPABASE_ANON_KEY: 'YOUR-ANON-OR-PUBLISHABLE-KEY',
  PASSCODE: 'ChangeThisPasscode'
};
```

> With the keys left empty, the app runs in **local-only mode**. Data stays in that browser's `localStorage` and nothing syncs.


Phones need an HTTPS link, so opening the file from a laptop won't work for the team.

### 3. Test before the event

1. Open the link on two phones and log in with different names.
2. Tick a task on one phone and watch it appear on the other.
3. Open **⋯ → Live Audit Log** and confirm your change is listed.

## How it works

```text
index.html (HTML + Tailwind CDN + vanilla JS)
      │  supabase-js
      ▼
Supabase
 ├─ records      key → jsonb   (every row, header, note, budget line, stamp)
 ├─ audit_logs   append-only   (user, role, section, item, action, old → new, time)
 └─ Realtime     pushes changes to every open phone
```

- **Keys:** records use keys such as `s6.3` (stage 6, row 3), `s6.hdr`, `s6.notes`, `bud.2`, `run.0` and `meta`.
- **Status logic:** setting **D** ticks the checkbox, and un-ticking a Done row returns it to **P**.
- **Audit:** the log is insert-only, because no update or delete policy exists.
- **Conflicts:** the last save wins, and the audit log shows who changed what.

## Project structure

```text
qubrix-control-desk/
├─ index.html   # entire app (UI, logic, all checklist content)
├─ setup.sql    # tables, RLS policies, realtime
└─ README.md
```

## Customising

- **Passcode:** change `CFG.PASSCODE`.
- **Checklist content:** edit the `SECS` array in `index.html`. Each stage is one `S(number, title, subtitle, 'item|item|item')` call.
- **Budget lines:** edit the `BUD` array and the `FEE` constant.
- **Colours:** edit the status classes `.st-P`, `.st-IP`, `.st-B` and `.st-D` in the `<style>` block.

## Security notes

- The passcode is checked **in the browser**, and the database policies allow the anon key to read and write. That suits a trusted internal event, but it is not strong security.
- Share the link and passcode only with organisers.
- Never put the `service_role` key in `index.html`.
- For stronger protection later, move the passcode check to a Supabase Edge Function and tighten the RLS policies.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Phones don't sync | Check that the URL and key are filled in, and that `CFG` was saved before deploying. |
| "Sync failed … row-level security" | Re-run `setup.sql`. |
| Changes only show after refresh | In Supabase, open **Database → Replication** and enable `records` and `audit_logs` for `supabase_realtime`. |
| "Already exists" error on the SQL | Harmless. The objects were already created. |
| App stopped working after a quiet week | Free Supabase projects pause after about 7 days of inactivity. Restore the project from the dashboard. |


## Credits

Built for **The Turing Club, AGMRCET** by the QUBRIX organising team. Checklist content is transcribed from the *QUBRIX Hackathon Operations Master Checklist & Responsibility Pack*.
