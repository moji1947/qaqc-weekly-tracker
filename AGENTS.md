# QA/QC Weekly Meeting Tracker — AI Context & Guidelines

## 1. Project Overview & Context
This repository contains a **single-file HTML web application (`index.html`)** built to replace an Excel-based weekly Minutes of Meeting (MOM) tracker (`QAQC_Weekly_MOM.xlsx`). 

It is used **live during weekly team meetings while screen-sharing**. A single typist (the meeting lead) inputs updates in real-time as team members speak. Edits persist to a shared backend (Supabase Postgres) with Realtime sync across all connected browser tabs.

---

## 2. Hard Constraints & Rules

> [!IMPORTANT]
> **VISIBLE UI TEXT MUST ALWAYS BE IN THAI**
> - All visible on-screen labels, buttons, headers, placeholders, tooltips, dialogs, and generated email text **MUST REMAIN IN THAI**.
> - English is strictly for codebase documentation, AI instructions, commit messages, and internal code comments. Do NOT translate any visible UI text to English.

### Architecture Constraints
- **Single-File Architecture**: The core app lives inside [`index.html`](file:///c:/Users/Moji/Projects/qaqc-weekly-tracker/index.html).
- **No Build Steps**: Vanilla HTML + CSS + JavaScript using CDN dependencies (`@supabase/supabase-js`). Do not introduce webpack, vite, npm build pipelines, or CSS preprocessors unless explicitly requested.
- **Design Mode**: Operates under **Operate-mode** principles (high data density, Excel-like table gridlines, clear contrast, tabular numbers, fast scanning, keyboard-accessible blur-to-save editing).

---

## 3. Domain Model & Business Logic

### Fixed Categories (3 items)
1. `1. Conzol & Project Registration Progress` (id: `cat1`)
2. `2. Standard Document QA/QC (Center/Site)` (id: `cat2`)
3. `3. Dashboard / AI Tools` (id: `cat3`)

### Fixed PIC Roster (Dropdown options)
- `-` (Unassigned)
- `QAQC System Admin`
- `QAQC Admin`
- `Tanawit Anantaphrut`
- `Pavinee Talthip`
- `Thanikorn Wangdee`
- `Pitchayapa Yaklai`
- `Supatchaya Ternpongsai`
- `Laddawan Suwankeeree`
- `Teerapong Chotiwannapruke`
- `Ekalak Wacharayingyong`

### Priorities
- `-`, `สูง` (High), `กลาง` (Medium), `ต่ำ` (Low)

### Core Workflows
1. **Single-Typist Editing**: Click cell -> edit value -> blur event triggers Supabase update (`updateItem`).
2. **Dual Status Visibility**: Both `Action Done` (green-tinted row) and `Next Step` (amber-tinted row) are visible simultaneously. Clicking the status button toggles between the two.
3. **Week Management & Auto Carry-Forward**:
   - Week switcher is a `<select class="qq2-week-select">` dropdown.
   - Clicking `+ สัปดาห์ใหม่` opens an inline form to name a new week.
   - When created, **all items with status `Next Step` (not `Action Done`) from the latest week are automatically copied into the new week**.
4. **Collapsible Extra Columns**:
   - Columns for `PIC 2`, `Priority`, `Due Date`, and `@tag` toggle are hidden by default and revealed via the toggle button `แสดงคอลัมน์เพิ่มเติม`.
5. **Email MOM Export Generator**:
   - Generates formatted plain text for email distribution.
   - Includes greeting, CC line, intro sentence naming current week, numbered categories with `-` bullet points for **Next Step items only**, and sign-off.
   - Items with the `@tag` button enabled will include `@PIC1` / `@PIC2` in the export text.
   - Email template settings (greeting, CC, signoff) are hardcoded in `EMAIL_TEMPLATE` in JS.

---

## 4. Backend & Database (Supabase)

### Active Project Configuration
- **Supabase Project URL**: `https://grtrpbnccqbtpaoovmoa.supabase.co`
- **Publishable / Anon Key**: `sb_publishable_7dJcpcsVd9PJ5GgVhlKbvQ_rrCzwWsq`
- **Schema Script**: [`database/schema.sql`](file:///c:/Users/Moji/Projects/qaqc-weekly-tracker/database/schema.sql) (Idempotent script with tables, RLS policies, Realtime publications, and seed data).

### Database Schema
- **`public.weeks`**: `(id text PK, label text, created_at timestamptz)`
- **`public.items`**: `(id text PK, week_id text FK -> weeks.id, cat text, no int, action text, pic1 text, pic2 text, status text, note text, priority text, due date, tag_pic bool, updated_at timestamptz)`
- **Security**: Open RLS policies (`using (true)`) matching the live meeting single-typist screen-share workflow.
- **Realtime**: Postgres changes on `weeks` and `items` are broadcast via Supabase Realtime channel `qq2-live`.

---

## 5. File Structure
```
qaqc-weekly-tracker/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Pages deployment workflow
├── database/
│   └── schema.sql              # Idempotent PostgreSQL schema & initial seed data
├── index.html                  # Entire single-file application (UI, styles, JS logic, Supabase client)
├── README.md                   # Human-facing project setup guide
├── AGENTS.md                   # AI Agent instructions & complete project context
└── CLAUDE.md                   # Claude / AI agent pointer
```
