# Helping Hands — Phase 2 Plan

> Assumes Phase 1 is running. Servers stay up. Work in place.

## Backend

- Install `python-slugify` into `.venv`.
- Create models: `post.py`, `event.py`, `project.py`, `partner.py`, `resource.py`. Each uses `TimestampMixin`, `slug` unique indexed, `is_published` bool default False.
- Register models in `models/__init__.py`.
- Add pydantic schemas for each (`*Read`, `*ListItem`).
- Add routers: `api/posts.py`, `api/events.py`, `api/projects.py`, `api/partners.py`, `api/resources.py`. Public GETs use pagination + `is_published=True` filter.
- Add `POST /api/events/{slug}/register` with rate limit.
- Register SQLAdmin views for each new model in `admin.py`.
- Mount routers in `main.py`.
- Seed script (`scripts/seed_p2.py`): insert 3 posts, 2 upcoming + 1 past event, 4 projects, 6 partners, 4 resources. Run once.

## Frontend

- Install `react-markdown remark-gfm date-fns`.
- Extend `lib/api.ts` with `posts`, `events`, `projects`, `partners`, `resources`.
- Add `components/content/MarkdownBody.tsx` (react-markdown wrapper with Tailwind typography classes).
- Add shared cards: `PostCard`, `EventCard`, `ProjectCard`, `PartnerLogo`, `ResourceRow`.
- Add `components/ui/Tabs.tsx`.
- Pages (each uses Header/Footer/ContactStrip):
  - `/blog/page.tsx` — SSR list, paginated.
  - `/blog/[slug]/page.tsx` — SSR detail, `generateMetadata` for OG.
  - `/events/page.tsx` — upcoming grid + past section.
  - `/events/[slug]/page.tsx` — detail + `EventRegistrationForm` (client component).
  - `/projects/page.tsx` — Tabs filter.
  - `/projects/[slug]/page.tsx`.
  - `/team/page.tsx` — static team cards with expandable bios (hardcoded data).
  - `/transparency/page.tsx` — table of resources filtered by category.
  - `/partners/page.tsx` — logos wall.
  - `/resources/page.tsx` — combined listing.
- Update `Header.tsx` + `Footer.tsx` nav arrays.
- Add `components/forms/EventRegistrationForm.tsx`.

## Verify

- Smoke: curl every new route (list + one detail + registration). All 2xx.
- Admin: visit `/admin`, verify new models appear, seeded rows visible.
- Browser: click through all new pages, confirm layouts match P1 aesthetic.
