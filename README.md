# Voting Machine

Secure, single-vote election platform consisting of a Vite + React frontend and a Node.js + Express backend. Voters authenticate with their mobile number, cast a vote for predefined candidates, and receive an SMS confirmation referencing “Ritunjai’s voting machine”. Data is persisted to `backend/data.json`, ensuring votes survive server restarts.

## Tech Stack

- Frontend: React, TypeScript, Vite, Tailwind, shadcn-ui
- Backend: Node.js, Express, file-based persistence (`backend/data.json`)
- Optional SMS delivery via Twilio

## Getting Started

```sh
# 1. Install dependencies
npm install

# 2. Start the backend (defaults to http://localhost:4000)
npm run server

# 3. In a separate terminal, start the frontend (defaults to http://localhost:5173)
npm run dev
```

Set `VITE_API_BASE_URL` in a `.env` file at the project root if you need to point the frontend at a different backend origin.

## Backend Configuration

Create `backend/.env` or use environment variables when launching the server. Supported values:

| Variable | Default | Purpose |
| --- | --- | --- |
| `PORT` | `4000` | HTTP port for the Express server |
| `ADMIN_SECRET` | `letmein` | Credential for `/api/admin/reset` |
| `ADMIN_MOBILE_NUMBER` | `9999999999` | Mobile number treated as the admin account |
| `CORS_ORIGIN` | `*` | Comma-separated allowlist for CORS |
| `SMS_COUNTRY_CODE` | `+91` | Prepended country code when sending SMS |
| `TWILIO_ACCOUNT_SID` | — | Enables real SMS delivery when combined with the values below |
| `TWILIO_AUTH_TOKEN` | — | Twilio authentication token |
| `TWILIO_FROM_NUMBER` | — | Registered Twilio sender number |

When Twilio credentials are missing, the backend logs the confirmation message instead of sending it.

## API Overview

| Method & Path | Description |
| --- | --- |
| `GET /api/health` | Liveness probe |
| `POST /api/login` | Body `{ mobileNumber }` → returns `{ user }` with session `token` |
| `GET /api/status?token=...` | Returns `{ user }`, confirming vote status |
| `GET /api/candidates` | Returns `{ candidates: [{ name, voteCount }] }` |
| `POST /api/vote` | Body `{ token, candidate }` → records a single vote and triggers SMS |
| `GET /api/results` | Returns sorted tally `{ candidates, totalVotes }` |
| `POST /api/admin/reset` | Body `{ adminSecret }` → resets votes and sessions |

All responses are JSON, and every voting/login action writes immediately to `backend/data.json`.

## Data Persistence

The backend maintains the following structure inside `backend/data.json`:

- `users`: keyed by mobile number with role, vote status, and history
- `sessions`: token → mobile number mappings
- `candidates`: vote counts for the predefined slate
- `votes`: audit log of recorded ballots

Deleting the file triggers automatic regeneration with zeroed vote totals.

## Deploying

Deploy the frontend with your preferred static hosting solution. Serve the backend on your Node.js platform of choice (e.g., Render, Railway, Fly.io) and set `VITE_API_BASE_URL` accordingly so the frontend can communicate with it.

For custom instructions or Lovable-specific deployment, visit the project dashboard: https://lovable.dev/projects/74812fff-ffe8-4d3d-a6a4-e43db56cae98
