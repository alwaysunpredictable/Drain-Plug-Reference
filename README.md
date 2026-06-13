# Drain Plug Reference

A single-page web app for quick lookup of oil drain plug torque specs, socket sizes, drain bolt thread sizes, and cartridge oil filter torque specs. Built for techs doing oil changes who need a fast, mobile-friendly reference at the bay.

The entire app — markup, styles, data, and logic — lives in one `index.html` file so it can be hosted on any static host (GitHub Pages, Netlify, etc.) with no build step or server.

## Features

### Drain Plug Specs
- Cascading filters (Year → Make → Model → Engine) narrow a database of vehicle drain plug specs.
- Each result shows torque (ft-lbs), socket size, drain bolt thread, OEM part/SAP numbers, whether a crush washer/gasket is required, and tech notes (warnings, OEM part numbers, special instructions).
- Vehicles with plastic oil pans or no drain plug (EVs) are marked "N/A — see notes" instead of a torque value.

### Cartridge Filter Torque
- Search by Service Champ, FRAM, Purolator, or NAPA part number, or by vehicle/application, to find cartridge filter housing torque specs (ft-lb and Nm).

### Suggestions
- Anyone can submit a correction, a missing vehicle/engine, or a general suggestion via a form (honeypot field and a 3-per-hour client-side rate limit help cut down on spam).
- Logged-in admins see a Suggestions tab listing pending/dismissed/done submissions and can mark them done, dismiss them, or reopen them.

### Admin Editing
- Admins log in (Supabase email/password auth) to add, edit, or delete custom entries directly from the Drain Plug Specs table.
- Custom entries are stored in Supabase and shared with every visitor — they aren't local to one browser.
- "Edit/Copy" on a built-in row pre-fills the editor so an admin can save a tweaked version as a new custom entry.

## Architecture

- **Frontend:** vanilla HTML/CSS/JS, no framework or build step.
- **Built-in data:** the drain plug specs and cartridge filter cross-reference tables are embedded directly in `index.html` as JS arrays (`DATA` and `CF_DATA`).
- **Backend:** [Supabase](https://supabase.com) provides:
  - Email/password authentication for admins, with session persistence in `localStorage` and automatic access-token refresh.
  - A `custom_entries` table for admin-added/edited vehicles, readable by everyone and writable by authenticated admins.
  - A `suggestions` table that the public can write to (via the suggestion form) and admins can read/update from the Suggestions tab.
- Row Level Security (RLS) policies on the Supabase tables control read/write access; the embedded Supabase key is the `anon` key, which is safe to expose as long as RLS is configured correctly.

## Usage

- **Everyone:** open `index.html`, pick a Year, Make, Model, and Engine to see drain plug specs, or switch to the Cartridge Filter tab and search by part number.
- **Admins:** click "Admin Login" in the header, sign in with a Supabase account, then use "+ Add Entry", "Edit/Copy", "Edit", and "Delete" on the Drain Plug Specs table, and review submissions under the Suggestions tab.

## Tech Stack

- HTML / CSS / Vanilla JavaScript
- Supabase (Auth + Postgres via PostgREST)
- LocalStorage (session persistence, suggestion rate-limiting)
