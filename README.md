# Helping Hands Association

Full-stack NGO website — donations, volunteers, events, blog, admin CMS.

## Stack

- **Frontend:** Next.js 16 + TypeScript + Tailwind CSS v4 + Fraunces/Instrument Sans
- **Backend:** FastAPI + SQLAlchemy 2 (async) + PostgreSQL + Alembic
- **Payments:** Stripe (one-time + recurring)
- **Email:** Resend
- **Admin:** SQLAdmin

## Run locally

### Backend

```bash
cd backend
python -m venv .venv
source .venv/Scripts/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt  # or manually: fastapi uvicorn sqlalchemy[asyncio] asyncpg aiosqlite alembic pydantic pydantic-settings pydantic[email] stripe resend sqladmin slowapi python-multipart itsdangerous python-slugify pytest pytest-asyncio httpx
cp .env.example .env
uvicorn app.main:app --reload --port 8000
```

Backend runs at http://localhost:8000 · Docs at `/docs` · Admin at `/admin` (default: `admin`/`admin`).

### Frontend

```bash
cd frontend
npm install
cp .env.local.example .env.local  # or set NEXT_PUBLIC_API_URL=http://localhost:8000
npm run dev
```

Frontend at http://localhost:3000.

## Project layout

```
backend/        FastAPI service — models, API, admin, services
frontend/       Next.js app — pages, components, lib
docs/           Specs + implementation plans
```

## Phases

- **Phase 1 (shipped):** Home / About / Programs / Get Involved / Contact / Donate + volunteer + contact + newsletter + Stripe + admin
- **Phase 2 (in progress):** Blog / Events + registration / Projects / Team / Transparency / Partners / Resources
- **Phase 3 (future):** User accounts, donor portal, paid event tickets, multi-language

See `docs/superpowers/specs/` for full design docs.
