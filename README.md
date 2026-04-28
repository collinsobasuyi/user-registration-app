# AuthBridge

A full-stack user authentication project built to demonstrate how a real web application connects a frontend, backend API, database, GitHub, and deployment platform.

**Stack:** Next.js 15 · FastAPI · PostgreSQL · Vercel · Railway

---

## Live URLs

### Frontend — Vercel

| Page | URL |
|---|---|
| Registration | https://authbridge.vercel.app |
| Login | https://authbridge.vercel.app/login |
| Account | https://authbridge.vercel.app/account |
| Admin dashboard | https://authbridge.vercel.app/admin |

### Backend — Railway

| Resource | URL |
|---|---|
| Health check | https://authbridge-production.up.railway.app |
| Swagger / API docs | https://authbridge-production.up.railway.app/docs |
| Register endpoint | https://authbridge-production.up.railway.app/api/register |
| Login endpoint | https://authbridge-production.up.railway.app/api/login |
| Users endpoint | https://authbridge-production.up.railway.app/api/users |
| Test 500 error | https://authbridge-production.up.railway.app/api/test-error |

---

## How It All Connects

```
Browser (Next.js — Vercel)
        |
        | HTTP  POST /api/register
        |       POST /api/login
        |       GET  /api/users/{id}
        |       PATCH /api/users/{id}
        |       DELETE /api/users/{id}
        v
FastAPI Backend (Railway)
        |
        | SQLAlchemy ORM
        v
PostgreSQL Database (Neon)
```

**User flow:**
1. User registers at `/` — account saved to PostgreSQL with bcrypt-hashed password
2. User logs in at `/login` — credentials verified, session stored in browser
3. User manages account at `/account` — edit name, change password, delete account
4. Admin views all users at `/admin` — full CRUD user management table

---

## Project Structure

```
AuthBridge/
├── frontend/
│   ├── app/
│   │   ├── layout.tsx              Root layout, Inter font, metadata
│   │   ├── page.tsx                Registration page (split-panel layout)
│   │   ├── login/
│   │   │   └── page.tsx            Login page
│   │   ├── account/
│   │   │   └── page.tsx            Account management (edit name, password, delete)
│   │   ├── admin/
│   │   │   └── page.tsx            Admin dashboard with user table
│   │   └── globals.css             Tailwind v4 import
│   ├── components/
│   │   ├── registration-form.tsx   Registration form with all states
│   │   └── login-form.tsx          Login form
│   ├── .env.local.example          Template — copy to .env.local
│   ├── package.json
│   ├── next.config.ts
│   └── tsconfig.json
│
├── backend/
│   ├── main.py                     FastAPI app — all routes, CORS, exception handler
│   ├── database.py                 SQLAlchemy engine, session, get_db dependency
│   ├── models.py                   User table definition
│   ├── schemas.py                  Pydantic schemas: register, login, update, response
│   ├── requirements.txt            Python dependencies
│   └── .env.example                Template — copy to .env
│
├── .gitignore
├── CLAUDE.md
└── README.md
```

---

## Running Locally

### Prerequisites

- Node.js 18+
- Python 3.11+
- A free [Neon](https://neon.tech) account (no local PostgreSQL needed)

---

### 1. Set up the database (Neon)

1. Go to [neon.tech](https://neon.tech) and sign up for free
2. Create a new project
3. Click **Connect** and copy your connection string — it looks like:
   ```
   postgresql://username:password@ep-xxx.region.aws.neon.tech/neondb?sslmode=require
   ```
4. Paste this into `backend/.env` in the next step

---

### 2. Start the backend

```bash
cd backend

# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env and add your Neon connection string:
# DATABASE_URL=postgresql://username:password@ep-xxx.region.aws.neon.tech/neondb?sslmode=require

# Start the server
uvicorn main:app --reload
```

API running at **http://localhost:8000** · Swagger docs at **http://localhost:8000/docs**

---

### 3. Start the frontend

```bash
cd frontend

# Install dependencies
npm install

# Set up environment variables
cp .env.local.example .env.local
# NEXT_PUBLIC_API_URL=http://localhost:8000  (already set)

# Start the dev server
npm run dev
```

Frontend running at **http://localhost:3000**

---

## API Endpoints

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| GET | `/` | Health check | None |
| POST | `/api/register` | Create new user | None |
| POST | `/api/login` | Verify credentials and return user | None |
| GET | `/api/users` | List all users | None |
| GET | `/api/users/{id}` | Get user by ID | None |
| PUT | `/api/users/{id}` | Full update (name + email) | None |
| PATCH | `/api/users/{id}` | Partial update (name, email, or password) | None |
| DELETE | `/api/users/{id}` | Delete user | None |
| GET | `/api/test-error` | Trigger deliberate 500 (dev/learning only) | None |

---

### POST /api/register

```json
{
  "full_name": "Jane Smith",
  "email": "jane@example.com",
  "password": "securepassword"
}
```

**201 Created:**
```json
{
  "id": 1,
  "full_name": "Jane Smith",
  "email": "jane@example.com",
  "created_at": "2026-04-27T17:05:17.164728Z"
}
```

**400 — duplicate email:**
```json
{ "detail": "An account with this email already exists." }
```

**422 — validation failure (e.g. password too short, empty name):**
```json
{ "detail": [{ "type": "string_too_short", "loc": ["body", "password"], ... }] }
```

---

### POST /api/login

```json
{
  "email": "jane@example.com",
  "password": "securepassword"
}
```

**200 OK:**
```json
{
  "id": 1,
  "full_name": "Jane Smith",
  "email": "jane@example.com",
  "created_at": "2026-04-27T17:05:17.164728Z"
}
```

**401 — wrong credentials:**
```json
{ "detail": "Invalid email or password." }
```

---

### PATCH /api/users/{id}

All fields optional — send only what you want to change:

```json
{ "full_name": "Jane Updated" }
```
```json
{ "password": "newpassword123" }
```
```json
{ "email": "new@example.com" }
```

---

## HTTP Status Codes

| Code | Meaning | When |
|---|---|---|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST /api/register |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Duplicate email |
| 401 | Unauthorised | Wrong login credentials |
| 404 | Not Found | User ID does not exist |
| 422 | Unprocessable Entity | Validation failure (missing field, short password, empty name) |
| 500 | Internal Server Error | Unhandled exception — safe message returned, no stack trace |

---

## Database Schema

```sql
CREATE TABLE users (
  id              SERIAL PRIMARY KEY,
  full_name       VARCHAR NOT NULL,
  email           VARCHAR UNIQUE NOT NULL,
  hashed_password VARCHAR NOT NULL,
  created_at      TIMESTAMPTZ DEFAULT NOW()
);
```

Passwords are hashed with **bcrypt** (used directly — passlib is not used). The plain-text password is never stored or returned.

---

## Environment Variables

### Backend — `backend/.env`

| Variable | Description |
|---|---|
| `DATABASE_URL` | Full Neon PostgreSQL connection string including `?sslmode=require` |

### Frontend — `frontend/.env.local`

| Variable | Description |
|---|---|
| `NEXT_PUBLIC_API_URL` | URL of the running FastAPI backend |
| `NEXT_PUBLIC_ADMIN_PASSWORD` | Password for the `/admin` dashboard (fails closed if not set) |

---

## Deployment

### Deploy order

1. Deploy backend to Railway first → get Railway URL
2. Deploy frontend to Vercel → set `NEXT_PUBLIC_API_URL` to Railway URL
3. Add Vercel URL to `allow_origins` in `backend/main.py` → push → Railway redeploys
4. Test end-to-end: Vercel form → Railway API → Neon database

### Backend → Railway

- Root directory: `backend`
- Start command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
- Environment variable: `DATABASE_URL` (full Neon string with `?sslmode=require`)

### Frontend → Vercel

- Root directory: `frontend`
- Framework: Next.js (auto-detected)
- Environment variables:
  - `NEXT_PUBLIC_API_URL` — Railway backend URL
  - `NEXT_PUBLIC_ADMIN_PASSWORD` — your chosen admin password

---

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| CORS error in browser | Vercel URL not in `allow_origins` | Add Vercel URL to `main.py` CORS list and redeploy Railway |
| Neon connection error | Neon idle timeout | `pool_pre_ping=True` handles reconnects automatically |
| 422 on registration | Missing field or short password | All fields required, password min 8 chars, name min 1 char |
| Build fails on Vercel | TypeScript error | Run `npm run build` locally first to catch errors |
| Admin page rejects all passwords | `NEXT_PUBLIC_ADMIN_PASSWORD` not set in Vercel | Add the env var in Vercel → Settings → Environment Variables |
| Login returns 401 | Wrong email or password | Message is intentionally the same for both to prevent user enumeration |

---

## What You Learn

- How a browser and a separate backend API communicate over HTTP
- How FastAPI validates requests using Pydantic schemas
- How SQLAlchemy maps Python classes to PostgreSQL tables
- Why passwords must be hashed with bcrypt and never stored plain
- How environment variables keep credentials out of source code
- The difference between GET, POST, PUT, PATCH, and DELETE
- How CORS works and why it must be configured for production
- How to deploy a full-stack app across Vercel, Railway, and Neon
- How to manage client-side session state without a backend auth library

---

Built by [Collins Obasuyi](https://collinsobasuyi.com)
