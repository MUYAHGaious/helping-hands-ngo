# Helping Hands Association — Phase 1 Implementation Plan

> **For agentic workers:** Use superpowers:subagent-driven-development or superpowers:executing-plans. Checkbox syntax tracks progress.

**Goal:** Ship a production-ready Phase 1 NGO site: 6 pages + donations + forms + admin.

**Architecture:** Next.js frontend (static + API calls) ↔ FastAPI backend ↔ Postgres. Stripe for payments, Resend for email, SQLAdmin for staff dashboard.

**Tech Stack:** Next.js 15, TypeScript, Tailwind v4, Fraunces + Instrument Sans, FastAPI, SQLAlchemy 2 async, Alembic, PostgreSQL, asyncpg, Stripe, Resend, SQLAdmin, pytest.

**Parallelism:** Frontend (Tasks 1-10) and Backend (Tasks 11-17) are independent — can be built simultaneously. Tasks 18-19 wire them together and depend on both.

**Palette tokens** (reused throughout):
- `--color-primary: #0E7C86` teal
- `--color-accent: #FF6B6B` coral
- `--color-warm: #F4E3C7` sand
- `--color-ink: #0E2A33` deep
- `--color-bg: #FBF9F3` off-white
- `--color-muted: #6B7A80`

---

## FRONTEND TRACK

### Task 1: Scaffold Next.js + fonts + tokens

**Files:**
- Create: `frontend/` (via create-next-app)
- Modify: `frontend/app/layout.tsx`, `frontend/app/globals.css`, `frontend/tailwind.config.ts`

- [ ] Run `npx create-next-app@latest frontend --typescript --tailwind --app --no-src-dir --no-eslint --turbopack --import-alias "@/*" --yes --use-npm` from `franks-ngo/`. (Since `frontend/` already exists and is empty, run inside the empty folder with `.` as target.)
- [ ] Replace `app/layout.tsx` with root layout that loads Fraunces (display) + Instrument Sans (body) via `next/font/google`, applies CSS variables `--font-display`, `--font-body`, sets `lang="en"`, base `<body class="bg-[--color-bg] text-[--color-ink] font-body antialiased">`.
- [ ] Replace `app/globals.css` with `@theme` block exposing palette tokens + font tokens to Tailwind v4.
- [ ] Set site metadata: title "Helping Hands Association — A world where no child's life is torn apart", description, Open Graph image placeholder.

### Task 2: Brand + decorative SVG assets

**Files:**
- Create: `frontend/components/brand/Logo.tsx`, `BrushStroke.tsx`, `BrushUnderline.tsx`, `AfricaSilhouette.tsx`, `AfricaMap.tsx`

- [ ] `Logo`: heart-in-hand SVG in teal + coral + wordmark "Helping Hands" in Fraunces. Accepts `className`.
- [ ] `BrushStroke`: reusable organic paint-splash SVG. Props: `color`, `variant` (1|2|3), `className`. Three path variants.
- [ ] `BrushUnderline`: hand-drawn underline for highlighting words. Props: `color`, `className`. Single SVG path.
- [ ] `AfricaSilhouette`: Africa continent outline SVG. Accepts `fill`, `className`. Source from wikimedia simplified path.
- [ ] `AfricaMap`: Africa with 8 location pins positioned by percentage. Props: `pinColor`, `fillGradient`.

### Task 3: Shared UI primitives + layout shell

**Files:**
- Create: `frontend/components/ui/Button.tsx`, `Container.tsx`, `Input.tsx`, `Textarea.tsx`, `Field.tsx`
- Create: `frontend/components/layout/Header.tsx`, `Footer.tsx`, `ContactStrip.tsx`, `CTABanner.tsx`

- [ ] `Button`: variants `primary` (teal fill, white), `accent` (coral fill), `outline` (border teal, transparent), `ghost` (text only). Sizes `sm|md|lg`. Supports `as="a"` or `as="button"`. Pill radius.
- [ ] `Container`: `max-w-[1200px] mx-auto px-6 md:px-10`.
- [ ] `Input`, `Textarea`, `Field`: form primitives with label, error message slot, focus ring in primary.
- [ ] `Header`: sticky top, Logo left, nav links (About / Programs / Get Involved / Contact), Donate button right. Mobile: hamburger → slide-down panel.
- [ ] `Footer`: Logo, nav links, Donate button, privacy/cookies links, copyright "© 2026 Helping Hands Association".
- [ ] `ContactStrip`: phone, email, 3 social icons (facebook/twitter/instagram) as a band above footer.
- [ ] `CTABanner`: dark photo-backgrounded section "Join our action! Everyone can help." with coral Donate + teal Volunteer buttons, brush splash edges. Used on multiple pages.

### Task 4: Home — Hero section

**Files:**
- Create: `frontend/components/home/Hero.tsx`, `frontend/public/hero-portrait.jpg` (download free portrait from Unsplash or use placeholder)

- [ ] Left column: eyebrow label, Fraunces H1 "A world where no child's life is torn apart by war." with coral BrushUnderline under "torn apart". Sub-copy paragraph. Primary Donate button + "Discover ↓" text link.
- [ ] Right column: AfricaSilhouette (sand-colored world-map faint bg behind it), portrait image clipped into Africa shape (use SVG `<clipPath>` or CSS `mask-image`). Overlay 3 BrushStroke decorations (coral, teal, sand).
- [ ] Stat callout: large "288" in Fraunces + "children die every day in Africa" caption, positioned beside the Africa.
- [ ] Staggered fade-up on mount (CSS `animation-delay` on each element).

### Task 5: Home — Services, Impact, Stories sections

**Files:**
- Create: `frontend/components/home/Services.tsx`, `ImpactCollage.tsx`, `Stories.tsx`
- Create: 3 portrait placeholders in `frontend/public/stories/`

- [ ] `Services`: 4 icon cards in a row — Healthy Food, **Education** (highlighted with sand background box + coral border), Pure Water, Medical. Each: Lucide icon (bowl-food / book-open / droplets / stethoscope) in teal, label (Fraunces), short copy.
- [ ] `ImpactCollage`: Left = 5 overlapping framed child-portrait images with coral/sand/teal brush splashes between them. Right = Fraunces H2 "What have we done with your help?", body copy, "About us →" link + primary Donate button.
- [ ] `Stories`: Section title "Learn the stories of those we've already helped" (Fraunces, coral BrushUnderline on "stories"). 3-card grid: Andrew / Sumaiya / Zakiya — each card = portrait, name, age, short bio, "Learn more →" link. Static (no carousel logic in P1).

### Task 6: Home — Stats + CTA + strip

**Files:**
- Create: `frontend/components/home/Stats.tsx`
- Modify: `frontend/app/page.tsx`

- [ ] `Stats`: Fraunces H2 "We are always where others need help." Left = 2×2 grid of stats (16,000 kg food / 2 mln L water / 1,694 volunteers / 830 teachers) — each with colored dot accent. Right = AfricaMap with teal + coral pins.
- [ ] Assemble `app/page.tsx`: `<Header/> <Hero/> <Services/> <ImpactCollage/> <Stories/> <Stats/> <CTABanner/> <ContactStrip/> <Footer/>`.

### Task 7: About page

**Files:**
- Create: `frontend/app/about/page.tsx`, `frontend/components/about/*.tsx`

- [ ] Hero: "Our story" + mission statement, small portrait with brush accents.
- [ ] Mission & Vision: two-column, each with icon + heading + copy.
- [ ] Values grid: 4 values (Integrity, Compassion, Action, Transparency) with icons.
- [ ] Team section placeholder: 4 portrait cards, name + role; note "Full team list coming soon."
- [ ] Transparency card: "Annual reports available on request — contact us."
- [ ] Reuse `CTABanner`, `ContactStrip`, `Footer`.

### Task 8: Programs page

**Files:**
- Create: `frontend/app/programs/page.tsx`

- [ ] Hero: "What we do", eyebrow "Our programs".
- [ ] 4 program sections (alternating image-left/image-right): Healthy Food · Education · Pure Water · Medical. Each: title (Fraunces), 2 paragraphs, photo with brush accents, inline Donate CTA.
- [ ] Stats reuse.
- [ ] CTABanner + ContactStrip + Footer.

### Task 9: Get Involved + Volunteer form UI

**Files:**
- Create: `frontend/app/get-involved/page.tsx`, `frontend/app/get-involved/volunteer/page.tsx`
- Create: `frontend/components/forms/VolunteerForm.tsx`, `NewsletterForm.tsx`

- [ ] `/get-involved`: hero "Everyone can help", two large option cards (Donate → `/donate`, Volunteer → `/get-involved/volunteer`), testimonial quote, NewsletterForm section.
- [ ] `/get-involved/volunteer`: VolunteerForm (name, email, phone, interests checkboxes: Food/Education/Water/Medical/Other, message textarea, submit).
- [ ] `VolunteerForm` uses `react-hook-form` + `zod`; on submit calls `api.volunteers.create()`. Shows success/error state inline.
- [ ] `NewsletterForm`: single email input + submit; calls `api.newsletter.subscribe()`.

### Task 10: Contact + Donate pages

**Files:**
- Create: `frontend/app/contact/page.tsx`, `frontend/app/donate/page.tsx`, `frontend/app/donate/success/page.tsx`, `frontend/app/donate/cancel/page.tsx`
- Create: `frontend/components/forms/ContactForm.tsx`, `DonateForm.tsx`

- [ ] `/contact`: hero "Get in touch", two-column: ContactForm (name/email/subject/message) left, contact info card right (phone, email, address, socials).
- [ ] `/donate`: hero "Your donation saves lives". DonateForm: amount buttons ($25 / $50 / $100 / $250 / custom), frequency toggle (One-time / Monthly), email input, Donate button → POST `/api/donations/checkout` → window.location = session URL.
- [ ] FAQ accordion 3 questions below form.
- [ ] `/donate/success`: confirmation message + links back home.
- [ ] `/donate/cancel`: "Donation cancelled — try again" link.

---

## BACKEND TRACK (can run parallel to frontend)

### Task 11: FastAPI scaffold with uv

**Files:**
- Create: `backend/pyproject.toml`, `backend/.env.example`, `backend/.gitignore`, `backend/app/__init__.py`, `backend/app/main.py`, `backend/app/core/config.py`, `backend/app/core/db.py`

- [ ] Initialize uv project: `cd backend && uv init --lib app && uv add fastapi 'uvicorn[standard]' sqlalchemy[asyncio] asyncpg alembic pydantic pydantic-settings 'fastapi-users[sqlalchemy]' stripe resend sqladmin python-multipart slowapi`
- [ ] Dev deps: `uv add --dev pytest pytest-asyncio httpx`
- [ ] `core/config.py`: `Settings(BaseSettings)` with DATABASE_URL, SECRET_KEY, STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET, RESEND_API_KEY, STAFF_NOTIFY_EMAIL, ADMIN_USER, ADMIN_PASSWORD, FRONTEND_ORIGIN, loaded from `.env`.
- [ ] `core/db.py`: async engine + `async_session_maker` + `get_session` dependency.
- [ ] `main.py`: FastAPI app, CORS middleware allowing `FRONTEND_ORIGIN`, `/api/health` endpoint, mount routers later.
- [ ] `.env.example` with all keys.

### Task 12: Alembic + base models

**Files:**
- Create: `backend/alembic.ini`, `backend/alembic/env.py`, `backend/alembic/versions/0001_initial.py`
- Create: `backend/app/models/__init__.py`, `base.py`, `volunteer.py`, `contact.py`, `donation.py`, `subscriber.py`

- [ ] `alembic init alembic`. Configure `alembic/env.py` to use async engine + import all models.
- [ ] `models/base.py`: `Base = declarative_base()`, `TimestampMixin` with `created_at`.
- [ ] `Volunteer`: id, created_at, name, email (indexed), phone, interests (String), message (Text), status (Enum `new|contacted|onboarded`, default new).
- [ ] `ContactMessage`: id, created_at, name, email, subject, message, handled (Bool default False).
- [ ] `Donation`: id, created_at, stripe_session_id (unique index), stripe_customer_id, email, amount_cents, currency, recurring (Bool), status (Enum `pending|succeeded|failed`, default pending).
- [ ] `NewsletterSubscriber`: id, created_at, email (unique), resend_contact_id nullable.
- [ ] `alembic revision --autogenerate -m "initial"` then `alembic upgrade head`.

### Task 13: Volunteer + Contact + Newsletter endpoints + tests

**Files:**
- Create: `backend/app/schemas/{volunteer,contact,subscriber}.py`
- Create: `backend/app/api/{volunteers,contact,newsletter}.py`
- Create: `backend/tests/conftest.py`, `test_volunteers.py`, `test_contact.py`, `test_newsletter.py`

- [ ] Pydantic schemas: `VolunteerCreate`, `VolunteerRead`, `ContactCreate`, `ContactRead`, `SubscriberCreate`. Email validation via `EmailStr`.
- [ ] POST `/api/volunteers` → validate → insert → send staff notification email (mockable) → 201 with VolunteerRead.
- [ ] POST `/api/contact` → insert → notify staff → 201.
- [ ] POST `/api/newsletter` → idempotent upsert by email → call Resend Audience API → 201 (always, even if duplicate).
- [ ] Rate limit each endpoint: 5/min/IP via slowapi.
- [ ] Mount routers in `main.py`.
- [ ] Tests: happy path creates row; invalid email → 422; rate limit → 429 after 6 requests; email service mocked.

### Task 14: Resend email service

**Files:**
- Create: `backend/app/services/email.py`, `backend/tests/test_email.py`

- [ ] `send_email(to, subject, html)` wrapper around `resend.Emails.send`. Uses `RESEND_API_KEY`.
- [ ] `notify_staff_volunteer(volunteer)`, `notify_staff_contact(msg)`, `send_donation_receipt(donation)` — compose HTML from inline templates.
- [ ] Tests mock `resend.Emails.send` and assert call args.

### Task 15: Stripe donation checkout + webhook

**Files:**
- Create: `backend/app/services/stripe_svc.py`, `backend/app/schemas/donation.py`, `backend/app/api/donations.py`, `backend/app/api/stripe_webhook.py`
- Create: `backend/tests/test_donations.py`, `test_webhook.py`

- [ ] `stripe_svc.create_checkout_session(amount_cents, email, recurring)`: builds Stripe Checkout Session (mode=payment or subscription with a $X/month Price created on-the-fly), returns `session.url` and `session.id`.
- [ ] POST `/api/donations/checkout`: validate body, create Donation row (status=pending, stripe_session_id from Stripe), return `{url}`.
- [ ] POST `/api/donations/webhook`: read raw body, verify signature via `stripe.Webhook.construct_event(payload, sig_header, STRIPE_WEBHOOK_SECRET)`. Handle `checkout.session.completed` → update Donation status=succeeded + send receipt. Handle `checkout.session.expired` / `payment_failed` → status=failed.
- [ ] Idempotency: upsert Donation row on stripe_session_id.
- [ ] Tests: mock Stripe client; valid body creates row + returns URL; webhook with valid signature updates row; invalid signature → 400.

### Task 16: SQLAdmin with basic auth

**Files:**
- Create: `backend/app/admin.py`, `backend/app/core/admin_auth.py`

- [ ] `AdminAuth(AuthenticationBackend)`: login validates against `ADMIN_USER`/`ADMIN_PASSWORD` env vars, stores session token, returns True on success.
- [ ] Register ModelViews for Volunteer, ContactMessage, Donation, NewsletterSubscriber with appropriate column_list and column_searchable_list.
- [ ] Mount at `/admin`.
- [ ] Manual verification: visit `/admin`, log in, view each model.

### Task 17: Backend run script + seed

**Files:**
- Create: `backend/Makefile` (or equivalent), `backend/scripts/seed.py`

- [ ] Make targets: `dev` (uvicorn with reload), `test` (pytest), `migrate` (alembic upgrade head), `seed` (python scripts/seed.py).
- [ ] `seed.py`: inserts 3 dummy volunteers, 2 contact messages, 1 donation for admin dashboard demo.

---

## INTEGRATION TRACK (depends on both)

### Task 18: Frontend API client + wire forms

**Files:**
- Create: `frontend/lib/api.ts`, `frontend/lib/env.ts`
- Modify: all form components

- [ ] `env.ts`: export `API_URL = process.env.NEXT_PUBLIC_API_URL`.
- [ ] `api.ts`: typed fetch client with methods `volunteers.create(data)`, `contact.create(data)`, `newsletter.subscribe(email)`, `donations.checkout({amount_cents, recurring, email})`. Each returns `{ ok, data?, error? }`.
- [ ] Wire VolunteerForm onSubmit → api.volunteers.create → show success toast + reset OR error message.
- [ ] Wire ContactForm the same way.
- [ ] Wire NewsletterForm (inline success state).
- [ ] Wire DonateForm → `api.donations.checkout` → on success, `window.location = res.url`.
- [ ] Configure frontend `.env.local` with `NEXT_PUBLIC_API_URL=http://localhost:8000` + `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`.

### Task 19: E2E verification + polish

- [ ] Boot backend: `make dev` (port 8000).
- [ ] Boot frontend: `npm run dev` (port 3000).
- [ ] Browser walk-through: every page loads, every form submits, every button links correctly.
- [ ] Stripe test: `stripe listen --forward-to localhost:8000/api/donations/webhook`. Donate $25 with test card `4242 4242 4242 4242`. Verify Donation row created + webhook updates status to succeeded + receipt email fires.
- [ ] Admin: visit `/admin`, log in, verify all submissions appear.
- [ ] Lighthouse on Home page — target Performance ≥ 85, Accessibility ≥ 95.
- [ ] Pixel parity check against reference webp — adjust spacing/colors if needed.
- [ ] Add `robots.txt`, `sitemap.xml`, favicon.
- [ ] README.md at root with setup + deploy instructions.

---

## Execution order

**Parallel waves:**
1. **Wave 1 (parallel):** Task 1 (FE scaffold) + Task 11 (BE scaffold)
2. **Wave 2 (parallel):** Tasks 2-3 (FE brand + shell) + Task 12 (BE models)
3. **Wave 3 (parallel):** Tasks 4-6 (FE Home) + Tasks 13-14 (BE form endpoints + email)
4. **Wave 4 (parallel):** Tasks 7-10 (FE other pages) + Tasks 15-16 (BE Stripe + admin)
5. **Wave 5 (serial):** Task 17 (BE scripts) → Task 18 (integration) → Task 19 (verify)

This compresses calendar time ~2x vs serial.
