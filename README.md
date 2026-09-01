# QA/QC Weekly Meeting Tracker

Single-file HTML app for the weekly QA/QC MOM meeting. No build step, no framework — `index.html` is the whole app, backed by a Supabase Postgres database so the team shares one live dataset instead of an Excel file.

## Setup (one time)

1. Create a free project at [supabase.com](https://supabase.com/dashboard) (New project → pick a name/region → wait ~2 min for it to provision).
2. In the new project, open **SQL Editor** → paste the contents of `database/schema.sql` → Run. This creates the `weeks` and `items` tables, seeds the two existing weeks, enables Realtime, and opens read/write access (no login — matches the original screen-share workflow).
3. Open **Settings → API** and copy the **Project URL** and the **anon / publishable key**.
4. In `index.html`, near the top of the `<script>` block, set:
   ```js
   const SUPABASE_URL = 'https://xxxxxxxx.supabase.co';
   const SUPABASE_ANON_KEY = 'sb_publishable_...';
   ```
5. Commit and push — GitHub Actions deploys `index.html` to GitHub Pages automatically on every push to `main`.

## What's included

- Excel-style table layout, single-typist editing (click a cell, type, blur to save).
- Both **Action Done** and **Next Step** rows shown per category, color-tinted.
- Week dropdown + "+ New week" that carries forward any open (Next Step) item.
- Collapsible extra columns (PIC 2 / Priority / Due Date / tag toggle).
- Live summary line (total / done / next-step / % complete).
- Email export matching the team's MOM template, with per-item @PIC tagging and copy-to-clipboard.
- Realtime sync: edits from any open tab appear for everyone else within a second or two (Supabase Realtime), without needing a page refresh.

## Data model

Two tables, see `database/schema.sql` for the full definition and RLS policies:

- `weeks (id, label, created_at)`
- `items (id, week_id, cat, no, action, pic1, pic2, status, note, priority, due, tag_pic, updated_at)`

## Security note

Access is intentionally open (no login), matching the original tool's "click and type during the meeting" simplicity — this repo is public and the anon key is safe to expose, but anyone with the deployed URL can read and write the data. If that stops being acceptable, add Supabase Auth and tighten the RLS policies in `database/schema.sql` to require an authenticated session.
