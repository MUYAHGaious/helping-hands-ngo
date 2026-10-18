# Helping Hands Association — Phase 1 Design Spec

**Date:** 2026-04-19
**Phase:** 1 of 3 (MVP — launch-ready core)
**Target launch:** 2-3 weeks of focused dev

---

## 1. Purpose

Build a production-ready NGO marketing + fundraising site for **Helping Hands Association**. Phase 1 delivers everything the organization needs to publicly launch, accept donations, and sign up volunteers. Content and community features (blog, events, member accounts) are Phase 2+ — out of scope here.

Visual reference: `original-f7b3f21f625ab7dafee62b0f803fa2c7.webp` (Kalypsia charity landing). We replicate the **layout and structure 1:1**, re-skin with a new palette, and expand from one page to five.

---

## 2. Scope (Phase 1 only)

**In scope:**
- 5 public pages: Home, About, Programs, Get Involved, Contact
- Online donation flow (Stripe: one-time + monthly recurring)
- Volunteer signup form → DB + notification email
- Contact form → DB + notification email
- Newsletter subscribe → Resend Audience
- SQLAdmin dashboard for staff to view/export submissions and donations
- Responsive (mobile + desktop), SEO meta, Open Graph
- Palette A: Ocean / Hope

**Explicitly out of scope (Phase 2+):**
- Blog / news CMS
- Events calendar and registration
- Per-program subpages, per-project impact story pages
- User accounts / donor portal / login
- Multi-language
- Team / Leadership / Transparency detail pages (placeholder sections only)
- Search

---

## 3. Design direction

### 3.1 Aesthetic

Follow the reference's **paintbrush + editorial serif** language. Hand-drawn energy (brush splashes, underline accents) paired with quiet, deliberate typography. Generous whitespace. Asymmetric hero with image cutout inside an Africa silhouette. Every page respects this visual system.

### 3.2 Color palette — "Ocean / Hope"

| Token | Hex | Use |
|---|---|---|
| `--color-primary` | `#0E7C86` | Teal — donate buttons, brand, key accents |
| `--color-accent` | `#FF6B6B` | Coral — volunteer CTAs, brush splashes, highlight |
| `--color-warm` | `#F4E3C7` | Sand — backgrounds, surface tones, brush splashes |
| `--color-ink` | `#0E2A33` | Deep teal-black — body text, nav, primary headlines |
| `--color-bg` | `#FBF9F3` | Off-white page background |
| `--color-muted` | `#6B7A80` | Secondary text, meta |

Brush-stroke shapes are rendered in the above colors (replacing reference yellow/green/orange).

### 3.3 Typography

Loaded via `next/font/google`:

- **Display:** Fraunces (variable serif, optical sizing enabled) — hero headlines, section titles
- **Body:** Instrument Sans — nav, body copy, buttons, forms

No Inter, no Roboto. The serif/sans pairing directly supports the editorial feel of the reference.

### 3.4 Motion

- Staggered fade-up on hero load (display text, image, stat callout arrive in sequence)
- Subtle hover lift on cards (`translateY(-3px)`, shadow bloom)
- Scroll-triggered reveal on story cards and stats (IntersectionObserver, CSS-only where possible)
- No page transitions in Phase 1

---

## 4. Site architecture

```
franks-ngo/                # folder name stays; site brand = Helping Hands Association
├── frontend/              (Next.js 15, TypeScript, Tailwind v4)
│   ├── app/
│   │   ├── layout.tsx     # root layout, fonts, nav, footer
│   │   ├── page.tsx       # Home
│   │   ├── about/page.tsx
│   │   ├── programs/page.tsx
│   │   ├── get-involved/
│   │   │   ├── page.tsx          # landing (volunteer + donate hub)
│   │   │   └── volunteer/page.tsx# volunteer signup form
│   │   ├── contact/page.tsx
│   │   ├── donate/page.tsx       # Stripe Checkout entry
│   │   └── api/ (not used — backend is FastAPI)
│   ├── components/
│   │   ├── layout/ (Header, Footer, Container)
│   │   ├── home/ (Hero, Services, Impact, Stories, Stats, CTABanner)
│   │   ├── forms/ (VolunteerForm, ContactForm, NewsletterForm, DonateForm)
│   │   ├── ui/ (Button, Input, Card)
│   │   └── brand/ (Logo, BrushStroke, BrushUnderline, AfricaSilhouette)
│   ├── lib/ (api.ts — typed fetch client for backend)
│   ├── public/ (images, brushes)
│   └── styles/globals.css
│
├── backend/               (FastAPI, Python 3.12+)
│   ├── app/
│   │   ├── main.py        # FastAPI app, CORS, routes mount
│   │   ├── core/
│   │   │   ├── config.py  # Pydantic Settings (env vars)
│   │   │   ├── db.py      # SQLAlchemy async engine + session
│   │   │   └── security.py
│   │   ├── models/        # SQLAlchemy ORM models
│   │   │   ├── volunteer.py
│   │   │   ├── contact.py
│   │   │   ├── donation.py
│   │   │   └── subscriber.py
│   │   ├── schemas/       # Pydantic request/response
│   │   ├── api/           # Route modules
│   │   │   ├── volunteers.py
│   │   │   ├── contact.py
│   │   │   ├── donations.py   # create Stripe session
│   │   │   ├── stripe_webhook.py
│   │   │   └── newsletter.py
│   │   ├── services/
│   │   │   ├── email.py   # Resend wrapper
│   │   │   └── stripe_svc.py
│   │   └── admin.py       # SQLAdmin registration
│   ├── alembic/
│   ├── alembic.ini
│   ├── pyproject.toml     # uv or pip deps
│   ├── .env.example
│   └── tests/
│
└── docs/superpowers/specs/
    └── 2026-04-19-franks-ngo-phase1-design.md  (this file)
```

---

## 5. Page specs

Each page uses the shared Header + Footer. All content is hardcoded in components for Phase 1 (no CMS). Text/images become CMS-editable in Phase 2.

### 5.1 Home (`/`)

Replicates the reference 1:1 in structure, re-skinned to palette A:

1. **Header** — "Helping Hands" wordmark + hand/heart icon in teal/coral, nav (About / Programs / Get Involved / Contact), Donate button (filled teal, top-right)
2. **Hero** — "A world where no child's life is torn apart by war." headline (Fraunces, coral brush underline on "torn apart"). Left: sub-copy + Donate (teal) + Discover (text link). Right: portrait inside Africa silhouette, coral/sand/teal brush splashes. Stat callout: "288 children die every day in Africa."
3. **Services row** — Healthy Food, Education (boxed/highlighted), Pure Water, Medical. Icon + label + short copy per item.
4. **"What have we done with your help?"** — Photo collage (overlapping framed portraits of children with coral/sand brush accents) on left. Right: copy + About us link + Donate CTA.
5. **"Learn the stories of those we've already helped"** — 3 portrait cards (Andrew/Sumaiya/Zakiya placeholders), age + short bio + Learn more →. Carousel dots/arrows (non-functional in P1 — static 3).
6. **Stats + Africa map** — "We are always where others need help." Stats grid: 16,000 kg food · 2M L water · 1,694 volunteers · 830 teachers. Right: Africa SVG with teal/coral location pins.
7. **CTA banner** — Full-width dark photo (overlay), "Join our action! Everyone can help." Coral Donate + Teal "Become a volunteer" buttons. Brush splashes on edges.
8. **Contact strip** — Phone, email, social icons.
9. **Footer** — Logo, nav, Donate, privacy/cookies, copyright.

### 5.2 About (`/about`)

- Hero: "Our story" + mission statement
- Mission & Vision (two-column with brush accent)
- "What drives us" values grid (3-4 values)
- Team placeholder section (3-6 portrait cards — "Leadership" title; actual team populated later)
- Transparency placeholder ("Reports coming soon" card linking to `/contact`)
- CTA banner reuse

### 5.3 Programs (`/programs`)

- Hero: "What we do"
- Program cards (3-4): Healthy Food, Education, Pure Water, Medical. Each card = icon + title + description + photo. Same four pillars as homepage services row, expanded.
- Impact stats block reuse
- CTA banner reuse

### 5.4 Get Involved (`/get-involved`)

- Hero: "Everyone can help"
- Two large option cards side-by-side:
  - **Donate** → links to `/donate`
  - **Volunteer** → links to `/get-involved/volunteer`
- Testimonial block (1-2 volunteer quotes)
- Newsletter signup section
- Volunteer form lives at `/get-involved/volunteer`

### 5.5 Contact (`/contact`)

- Hero: "Get in touch"
- Two-column: form (left) + contact info card (right, with phone/email/address/social)
- Embedded map placeholder (static image or OpenStreetMap iframe — no API key)
- CTA banner reuse

### 5.6 Donate (`/donate`)

- Hero: "Your donation saves lives"
- Amount selector: $25, $50, $100, $250, custom
- Frequency toggle: One-time / Monthly
- Submit → POST `/api/donations/checkout` → redirect to Stripe Checkout
- On return: `/donate/success` (thank-you) or `/donate/cancel`
- FAQ accordion (3-4 questions: tax-deductible, where my money goes, receipts)

---

## 6. Backend API surface (Phase 1)

All routes prefixed `/api`. JSON in/out. CORS configured for the frontend origin.

| Method | Path | Purpose | Body |
|---|---|---|---|
| POST | `/api/volunteers` | Create volunteer application | name, email, phone, interests, message |
| POST | `/api/contact` | Create contact message | name, email, subject, message |
| POST | `/api/newsletter` | Subscribe email (idempotent) | email |
| POST | `/api/donations/checkout` | Create Stripe Checkout session | amount_cents, recurring (bool), email |
| POST | `/api/donations/webhook` | Stripe webhook — creates Donation row on `checkout.session.completed` | Stripe event |
| GET | `/api/health` | Liveness | — |

All POST endpoints that collect PII are **rate-limited** (slowapi middleware: 5/min/IP) and validate email server-side.

### 6.1 Data models (SQLAlchemy)

```python
# Volunteer
id: int (pk)
created_at: datetime
name: str
email: str (indexed)
phone: str | None
interests: str  # comma-separated tags
message: str | None
status: enum(new, contacted, onboarded)  # staff sets via admin

# ContactMessage
id, created_at, name, email, subject, message, handled: bool

# Donation
id, created_at
stripe_session_id: str (unique)
stripe_customer_id: str | None
email: str
amount_cents: int
currency: str  # "usd"
recurring: bool
status: enum(pending, succeeded, failed)
# webhook fills in stripe_session_id on create, then status on completion

# NewsletterSubscriber
id, created_at, email (unique), resend_contact_id: str | None
```

### 6.2 SQLAdmin

Registers ModelView for Volunteer, ContactMessage, Donation, NewsletterSubscriber. Mounted at `/admin`, protected by HTTP Basic auth (single staff credential in env for Phase 1 — replaced by proper auth in Phase 3). Columns show summary fields; detail view shows all.

---

## 7. Payment flow (Stripe)

1. User picks amount + frequency on `/donate`, submits form
2. Frontend POSTs to `/api/donations/checkout` → backend creates `Donation` row (status=pending) and a Stripe Checkout Session (mode=payment or mode=subscription), returns session URL
3. Frontend redirects browser to session URL
4. User pays on Stripe
5. Stripe redirects to `/donate/success?session_id=...`
6. Stripe sends webhook → `/api/donations/webhook` verifies signature, updates Donation row to `succeeded`, triggers Resend email receipt
7. Success page shows confirmation; failed/canceled → `/donate/cancel`

Webhook signature verification uses `STRIPE_WEBHOOK_SECRET`. Idempotent — duplicate webhook events upsert on `stripe_session_id`.

---

## 8. Email

Resend for all transactional email:
- Volunteer signup → confirmation to volunteer + notification to staff inbox
- Contact form → notification to staff inbox (no auto-reply in P1)
- Donation succeeded → receipt to donor
- Newsletter → handled by Resend Audience (no email from us)

Staff inbox = `STAFF_NOTIFY_EMAIL` env var.

---

## 9. Environment configuration

**Frontend (`.env.local`):**
```
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
```

**Backend (`.env`):**
```
DATABASE_URL=postgresql+asyncpg://...
SECRET_KEY=...
STRIPE_SECRET_KEY=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...
RESEND_API_KEY=re_...
STAFF_NOTIFY_EMAIL=staff@helpinghands.org
ADMIN_USER=admin
ADMIN_PASSWORD=...
FRONTEND_ORIGIN=http://localhost:3000
```

---

## 10. Testing

- **Backend:** pytest + httpx `AsyncClient`. Cover each POST endpoint: valid input creates row + sends email (mocked), invalid input 422, rate limit triggers 429, webhook signature mismatch 400.
- **Frontend:** Playwright smoke on the five pages + form submit happy paths. Visual regression out of scope for P1 — manual pixel-parity review against the reference.
- **Stripe:** test mode end-to-end with `stripe listen` forwarding to local webhook.

---

## 11. Build order

Dependencies flow: design system → page shells → backend endpoints → integrate forms → Stripe → deploy.

1. Scaffold Next.js frontend, configure fonts + tokens + globals
2. Build shared components (Header, Footer, Button, BrushStroke, AfricaSilhouette, Logo)
3. Build Home page section-by-section (Hero → Services → Impact → Stories → Stats → CTA → Contact)
4. Build About, Programs, Get Involved, Contact pages reusing components
5. Scaffold FastAPI backend with uv, Alembic baseline migration, models, CORS, config
6. Implement `/api/volunteers`, `/api/contact`, `/api/newsletter` with tests
7. Wire forms on frontend to backend, test end-to-end
8. Implement Stripe checkout + webhook, Donate page
9. Configure Resend, add email templates, wire receipts + staff notifications
10. Register SQLAdmin, protect with basic auth
11. Deploy frontend to Vercel, backend+Postgres to Railway (or Render), configure Stripe webhook URL
12. End-to-end smoke in production

---

## 12. Open questions to resolve before Phase 2

- Final mission statement, taglines, and copy for each page (official name is **Helping Hands Association**)
- Real mission statement, program descriptions, team bios, photography
- Stripe account credentials (test + live)
- Resend account + verified sending domain
- Production domain + DNS
- Who are the initial staff admin users (emails + names)

None of these block Phase 1 dev — they're content/credentials plugged in during deploy.

---

## 13. What Phase 2 will add (reference only, not built here)

Blog / news (MDX files committed to repo initially, then migrate to CMS models), events + registration, project detail pages, team pages, partners wall, resources/reports library, per-program subpages.

Phase 3: user accounts, donor portal, full admin CRUD for pages and posts (replaces SQLAdmin with custom dashboard), multi-language, analytics dashboard.
