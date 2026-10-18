# Helping Hands Association — Phase 2 Design Spec

**Date:** 2026-04-19
**Builds on:** Phase 1 (live: 6 public pages + forms + Stripe + admin)

## Scope (Phase 2)

1. **Blog / News** — article list + detail, CMS-editable via SQLAdmin. Markdown body.
2. **Events** — upcoming/past events, event detail, registration form (free RSVP in P2; paid tickets = P3).
3. **Projects / Impact Stories** — ongoing and completed projects, each with its own page.
4. **Team** — leadership detail page with bios.
5. **Transparency** — reports page listing uploaded PDFs with year + category + download link.
6. **Partners** — logos wall with links.
7. **Resources** — aggregated page for reports + media + downloads (reuses Transparency data).
8. **Nav expansion** — Header + Footer include new routes.

**Out of scope (Phase 3):** user accounts, donor portal, paid event tickets, full CMS for pages, multi-language.

## Data models (new)

```python
# Post (blog/news)
id, slug (unique), title, summary, body_md, cover_image_url,
author, published_at, is_published (bool), created_at

# Event
id, slug (unique), title, summary, body_md, cover_image_url,
location, starts_at, ends_at, capacity (nullable), is_published, created_at

# EventRegistration
id, event_id (fk), name, email, attendees (int), notes, created_at

# Project
id, slug (unique), title, summary, body_md, cover_image_url,
country, status (enum: ongoing|completed), year_started, is_published, created_at

# Partner
id, name, logo_url, link_url, sort_order, is_published

# Resource  (reports / PDFs / media)
id, title, description, file_url, category (enum: annual_report|financial|media|other),
year, is_published, created_at
```

## API (new endpoints)

Public (GET only, no auth):
- `GET /api/posts?page=1&limit=10` — paginated list (is_published=True, sorted by published_at desc)
- `GET /api/posts/{slug}` — single post detail
- `GET /api/events?filter=upcoming|past` — list
- `GET /api/events/{slug}` — detail
- `GET /api/projects?status=ongoing|completed` — list
- `GET /api/projects/{slug}` — detail
- `GET /api/partners` — list (sorted by sort_order)
- `GET /api/resources?category=...` — list

Write:
- `POST /api/events/{slug}/register` — RSVP (rate-limited 5/min)

Admin (via SQLAdmin): full CRUD for all new models.

## Frontend pages (new)

- `/blog` — post list, paginated, featured card for most recent
- `/blog/[slug]` — article detail with markdown rendering
- `/events` — upcoming grid + past accordion
- `/events/[slug]` — detail + RegistrationForm
- `/projects` — card grid, filter tabs (All / Ongoing / Completed)
- `/projects/[slug]` — detail with hero + body + gallery
- `/team` — grid of leadership cards, expandable bios
- `/transparency` — annual reports + financials table, PDF download links
- `/partners` — logos wall, links out
- `/resources` — combined list: reports + media files, filterable by category

## Shared components (new)

- `components/content/MarkdownBody.tsx` — renders markdown using `react-markdown` + `remark-gfm`
- `components/content/PostCard.tsx`, `EventCard.tsx`, `ProjectCard.tsx`, `PartnerLogo.tsx`
- `components/forms/EventRegistrationForm.tsx`
- `components/ui/Tabs.tsx` — simple tab component for list filters

## Nav update

Header now shows: About · Programs · Projects · Events · Blog · Get Involved · Contact. **Donate** stays in right-side CTA slot. Mobile menu accommodates the longer list.

Footer mirrors + adds Transparency · Partners · Resources.

## Dependencies (new)

Backend: `python-slugify`
Frontend: `react-markdown`, `remark-gfm`, `date-fns`

## Build order

1. Add models + migrations
2. Add public GET routes for all content (with seed data so frontend can be built in parallel)
3. Add EventRegistration POST
4. Register SQLAdmin views for new models
5. Frontend: MarkdownBody + shared cards
6. Blog pages → Events pages → Projects pages → Team/Transparency/Partners/Resources
7. Nav expansion
8. Smoke test all routes
