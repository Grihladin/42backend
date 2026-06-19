# 42Connect

> **Hackathon project — built at a 42 Heilbronn hackathon.**

42Connect helps 42 students find the right classmate to ask for help. It signs a student in through 42 Intra, imports their current and completed projects, and matches active projects with students who have already finished them and opted in to help.

This repository is a time-boxed hackathon prototype. It demonstrates the product idea and a working full-stack implementation rather than a production-ready social platform.

## The problem

At 42, students learn from one another, but finding someone with relevant experience often depends on already knowing whom to ask. 42Connect turns project history into a lightweight peer-support network:

1. Sign in with a 42 Intra account.
2. Sync current and completed projects from the 42 API.
3. Mark yourself as available to help and describe your current “vibe.”
4. Find students who completed a project you are currently working on.
5. Discover peers with similar interests through vibe matching.

## What the prototype includes

- 42 OAuth2 authentication and signed cookie sessions
- Automatic profile, cursus, and project synchronization from the 42 API
- Dashboard for current projects, completed projects, marks, and progress
- Helper matching by shared project
- Opt-in “ready to help” preference
- Text-based vibe matching between students
- PostgreSQL persistence
- Dockerized frontend, backend, and database

## Technology stack

| Area | Technologies |
| --- | --- |
| Frontend | Next.js 14, React 18, TypeScript, CSS |
| Backend | Python 3.11, FastAPI, Pydantic |
| Authentication | 42 Intra OAuth2, Authlib, signed HTTP-only cookies |
| Data | PostgreSQL 16, SQLAlchemy 2, asyncpg |
| Integration | 42 API, HTTPX |
| Infrastructure | Docker, Docker Compose |

## Architecture

```text
Next.js frontend
        |
        | REST + session cookie
        v
FastAPI backend ---- OAuth/API requests ----> 42 Intra
        |
        | SQLAlchemy + asyncpg
        v
   PostgreSQL
```

The backend imports a student's 42 profile and project records after login. For each active project, it searches the local database for opted-in students who have completed the same project. See [`docs/schema.md`](docs/schema.md) for the data model.

## Run locally

### 1. Configure the backend

Copy the provided template:

```bash
cp backend/.env.example backend/.env
```

Set at least:

```dotenv
FORTYTWO_CLIENT_ID=your_42_oauth_client_id
FORTYTWO_CLIENT_SECRET=your_42_oauth_client_secret
FORTYTWO_REDIRECT_URI=http://localhost:8000/auth/callback
FRONTEND_APP_URL=http://localhost:3000
SESSION_SECRET_KEY=replace_with_a_long_random_value
DATABASE_URL=postgresql+asyncpg://app:app@db:5432/fortytwo_app
```

The callback URL must also be registered in your 42 API application.

### 2. Use local service URLs

The current `docker-compose.yml` contains the original demo server address. Before running locally, replace `http://168.119.52.144:8000` with `http://localhost:8000` and `http://168.119.52.144:3000` with `http://localhost:3000`.

### 3. Start the application

```bash
docker compose up --build
```

Open <http://localhost:3000>. The API is available at <http://localhost:8000>.

## Repository structure

```text
.
├── backend/            # FastAPI API, OAuth flow, sync logic, and models
├── frontend/           # Next.js dashboard and helper views
├── docs/schema.md      # Database schema documentation
├── docker-compose.yml  # PostgreSQL, backend, and frontend services
└── 42Quackform/        # Separate experimental prototype retained from the event
```

## Hackathon status

The core demo flow is implemented. Before production use, the project would need database migrations, automated tests, improved secret management, HTTPS, stronger deployment configuration, and a proper way for matched students to contact one another.
