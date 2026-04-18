# JobMatch Platform

A full-stack job matching platform where **clients** post jobs and **workers** apply, chat, and get hired — with real-time messaging and notifications.

> **Live app:** [https://job-match-fn.vercel.app](https://job-match-fn.vercel.app)

---

## Monorepo Structure

```
Job_matching/
├── job-matching-bn/     # Express + TypeScript REST API & WebSocket server
└── job-matching-frontend/  # React + Vite single-page application
```

---

## Tech Stack

### Backend (`job-matching-bn`)
| | |
|---|---|
| Runtime | Node.js 20 |
| Framework | Express 4 |
| Language | TypeScript 5 |
| ORM | Prisma 7 |
| Database | PostgreSQL (Neon) |
| Real-time | Socket.IO 4 |
| Auth | JWT + bcrypt |
| Deployed on | Render |

### Frontend (`job-matching-frontend`)
| | |
|---|---|
| Framework | React 18 |
| Language | TypeScript 5 |
| Build Tool | Vite 5 |
| Styling | Tailwind CSS 3 |
| Routing | React Router v6 |
| HTTP | Axios |
| Real-time | Socket.IO Client 4 |
| Deployed on | Vercel |

---

## Features

- **Authentication** — Separate registration paths for each role; JWT-based login shared by both
  - **Workers** → `/register` — creates a `WORKER` account, lands on the job board after sign-up
  - **Clients** → `/register-client` — creates a `CLIENT` account, lands on `/dashboard` after sign-up
- **Job Board** — Clients post jobs with budget and tags; workers browse and filter by tags and budget
- **Applications** — Workers apply for jobs; clients accept, reject, or progress applications through a status pipeline (`PENDING → MATCHED → IN_PROGRESS → COMPLETED`)
- **Real-time Messaging** — Per-application chat rooms powered by Socket.IO
- **Notifications** — In-app notifications pushed in real time on application status changes
- **Dashboard** — Role-aware dashboard with paginated jobs table (clients) and applications grid (workers)

---

## Lighthouse Scores

Audited on the production build (`http://localhost:4173/` — Vite preview).

| Category | Score |
|---|:---:|
| Performance | 🟢 **96** |
| Accessibility | 🟢 **90** |
| Best Practices | 🟢 **100** |
| SEO | 🟢 **92** |

![Lighthouse results](lighthouse.png)

> **Note:** The audit was run with IndexedDB data present in the browser, which may slightly inflate or deflate Performance. Re-running in an incognito window may yield marginally different scores.

---

## Quick Start

### Prerequisites
- Node.js 20+
- PostgreSQL database (or [Neon](https://neon.tech) free tier)

### 1. Clone the repo

```bash
git clone https://github.com/pacifiquemboni/job-matching-platform.git
cd Job_matching
```

### 2. Set up the backend

```bash
cd job-matching-bn
npm install
```

Create `job-matching-bn/.env`:

```env
DATABASE_URL=postgresql://user:password@host/dbname
JWT_SECRET=your_jwt_secret_here
PORT=3010
CORS_ORIGIN=*
```

```bash
npx prisma migrate dev      # run migrations
npm run prisma:seed         # optional: seed 110 sample jobs
npm run dev                 # start dev server on :3010
```

### 3. Set up the frontend

```bash
cd ../job-matching-frontend
npm install
```

Create `job-matching-frontend/.env`:

```env
VITE_API_URL=http://localhost:3010/api
VITE_SOCKET_URL=http://localhost:3010
```

```bash
npm run dev                 # start Vite dev server on :5173
```

Open [http://localhost:5173](http://localhost:5173).

---

## Environment Variables

### Backend (`job-matching-bn/.env`)

| Variable | Description |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string |
| `JWT_SECRET` | Secret key for signing JWTs |
| `PORT` | Server port (default `3010`) |
| `CORS_ORIGIN` | Allowed CORS origin (use `*` for development) |

### Frontend (`job-matching-frontend/.env`)

| Variable | Description |
|---|---|
| `VITE_API_URL` | Full URL to the backend API (e.g. `https://your-api.onrender.com/api`) |
| `VITE_SOCKET_URL` | Full URL to the backend for Socket.IO (e.g. `https://your-api.onrender.com`) |

---

## Deployment

### Backend → Render
1. Create a new **Web Service** on [Render](https://render.com) pointing to `job-matching-bn/`
2. **Build command:** `npm install && npm run build`
3. **Start command:** `node dist/index.js`
4. Add all environment variables in the Render dashboard

### Frontend → Vercel
1. Create a new project on [Vercel](https://vercel.com) pointing to `job-matching-frontend/`
2. **Framework preset:** Vite
3. Add `VITE_API_URL` and `VITE_SOCKET_URL` in Vercel environment variables
4. The included `vercel.json` handles SPA routing automatically

Both services auto-deploy on every push to `main`.

---

## API Overview

Base URL: `/api`

| Resource | Prefix | Notes |
|---|---|---|
| Auth | `/api/auth` | `POST /register`, `POST /login` |
| Jobs | `/api/jobs` | Public browse; CLIENT-only create/update |
| Applications | `/api/applications` | WORKER applies; CLIENT manages status |
| Messages | `/api/messages` | Per-application chat; authenticated |
| Notifications | `/api/notifications` | Per-user; authenticated |

See [job-matching-bn/README.md](job-matching-bn/README.md) for the full API reference.

---

## Project Docs

- [Backend README](job-matching-bn/README.md) — API reference, Socket.IO events, data models, scripts
- [Frontend README](job-matching-frontend/README.md) — Pages, routing, component structure, deployment
