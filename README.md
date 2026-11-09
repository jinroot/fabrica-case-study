# Made By Jinjin — Case Study

A full-stack e-commerce platform I designed and built for my own small 3D-printing business. The source is private because it's a live business, so this repo documents the **architecture, decisions and engineering practices** instead of the code. Happy to walk through the real code in an interview.

**Role:** sole engineer (design, backend, frontend, database, deployment, testing)
**Stack:** Next.js 16 · TypeScript · Supabase (Postgres, Auth, Storage) · Netlify · Vitest · Docker
**Size:** 65+ commits · 27 SQL migrations · 30+ test files

## What it does

- **Bilingual storefront (English / Arabic)** with proper RTL support.
- **Catalog and quoting:** products with options, variations, sub-categories, deals and coupons; custom-print requests with 3D file uploads.
- **Checkout** with server-verified totals and deposit/receipt confirmation.
- **Admin back office:** catalog editing, order handling, manual orders, job costing, popups, a photo studio, and cookie-free analytics.
- **Optional customer accounts** with Google sign-in.

## Architecture decisions worth reading

| Decision | Why |
|---|---|
| **Type-checked translations.** The Arabic dictionary is typed against the English one. | A missing translation key fails `typecheck` instead of shipping a broken page. A parity test backs this up. |
| **Pricing is one pure, tested module** used in the browser (for previews) and on the server (for the real total). | The client can never decide the price. Order placement re-reads current rows and re-quotes. |
| **Row-level security on every table.** The browser only holds the anon key. | All writes go through Server Actions that verify the admin session. A leaked client bundle can't write data. |
| **Business-critical steps are Postgres functions** (place order, confirm deposit, set order status, save/duplicate product). | Multi-step changes are atomic, so a failure mid-way can't leave half an order. |
| **Direct-to-storage uploads via signed URLs**, then server-side validation of real file bytes and size. | Large 3D files never pass through the app server, and a renamed or malicious file (fake STL, hostile SVG) is rejected on content, not extension. |
| **Photos re-encoded in the browser.** | Strips EXIF metadata (privacy) and shrinks uploads. |
| **One cache tag for storefront reads**, refreshed on every admin save. | Fast pages, and edits appear on the next load without manual invalidation. |
| **A build step that fails if a secret reaches the browser bundle.** | Turns "don't leak keys" from a habit into a guardrail. |
| **Daily encrypted backups via GitHub Actions** and a scheduled keep-alive. | Recoverable on the free tier, with a documented restore process. |

## Testing strategy

- **Fast tests** (no network, no secrets, no live data) run on every Netlify build.
- **Database tests** run against real Postgres semantics.
- **Server-action tests** cover checkout, admin access, uploads and account linking.
- **End-to-end tests** drive the whole shop in a real browser against a private Supabase instance in Docker, and refuse to run against a database that holds real orders.
- **Read-only smoke test** safe to run against production.

## What I'd highlight to a team

- Designing for **safe failure**: server-side price truth, atomic DB functions, RLS, content-based upload checks.
- **Bilingual/RTL** done as an engineering problem (types and tests), not as copy-paste.
- **Operational maturity** for a one-person project: migrations, seed scripts, backups, usage reports, documented infrastructure.
- Built with AI coding agents (Claude Code) using scoped specs and full review, with the test suite as the safety net.

## Contact

[LinkedIn](https://www.linkedin.com/in/johnashraf) · [GitHub profile](https://github.com/jinroot) · john.ashraf.g@gmail.com
