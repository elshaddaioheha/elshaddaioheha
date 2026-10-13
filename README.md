# Oheha Ebibi

Full-stack and backend engineer. I build production web applications, APIs, data pipelines, and ML-backed services for founders and small teams.

**Available for freelance work** · [ohehaebibi.dev](https://ohehaebibi.dev) · [elshaddaioheha@gmail.com](mailto:elshaddaioheha@gmail.com) · [@0hehaebib1](https://x.com/0hehaebib1)

---

### About

Four years of shipping software. Currently backend at **AZ-Genes** and full-stack at **Oloja Foundation**, with outside contracts running in parallel.

I do my best work where the numbers have to be right — costs, payments, bookings, clinical inputs — and where the team needs an engineer who scopes tightly, ships in reviewable slices, and leaves documentation behind for whoever picks the work up next.

Off the keyboard, I play bass.

---

### Stack

My default production stack is **Next.js + TypeScript** on the front, **Node.js** or **Python (Flask / FastAPI)** behind it, and **PostgreSQL** or **MongoDB** as the system of record — deployed on **AWS**, **Supabase**, or a managed platform depending on the project.

| Layer | Tools | Where I reach for it |
| --- | --- | --- |
| **Frontend** | Next.js (App Router), TypeScript, React, Tailwind CSS, TanStack Query, React Hook Form + Zod, MapLibre | Production web apps, dashboards, map-heavy and data-dense interfaces |
| **Mobile** | React Native (Expo) | Cross-platform apps sharing types with the web client |
| **Backend — Node** | Node.js, TypeScript, Fastify, NestJS, Express, REST & GraphQL, OpenAPI/Swagger | Core product APIs, auth, multi-tenant business logic |
| **Backend — Python** | Flask, FastAPI | ML inference services, data tooling, lightweight internal APIs |
| **Databases** | PostgreSQL (Prisma), MongoDB, SQLite | Postgres for relational, transactional data; MongoDB for document-shaped data; SQLite for Python tooling, prototypes, and local-first apps |
| **Backend-as-a-service** | Supabase (Postgres, Auth, Storage, Row-Level Security) | Shipping auth and data fast without giving up a real Postgres underneath |
| **Caching & jobs** | Redis, BullMQ | Caching, rate limiting, sessions, scheduled and background jobs |
| **Machine learning** | PyTorch, Hugging Face Transformers | Fine-tuned models served behind an API |
| **Cloud & infra** | AWS (EC2, S3, IAM, CloudWatch), Docker, Docker Compose, Vercel, Railway | EC2 for long-running services, S3 for storage, containers everywhere |
| **Quality & workflow** | Vitest, GitHub Actions, ESLint, Prettier, Husky, Commitlint, pnpm workspaces | Format, lint, typecheck and test gates on every PR |

---

### How a project moves end to end

1. **Scope** — Agree on the problem, the users, and what "done" means. Write it down.
2. **Model the data** — Design the schema first. Constraints, indexes, tenancy and transaction boundaries are decided before any endpoint exists.
3. **Define contracts** — Typed API contracts (Zod schemas on the TypeScript side, Pydantic models on the Python side) so frontend and backend can move in parallel.
4. **Build in vertical slices** — Each slice runs UI → API → database and is deployable on its own. Weekly check-ins review working software, not status reports.
5. **Harden** — Pure, unit-tested business logic; idempotent writes; atomic operations where concurrency demands it; Redis where latency demands it; secrets kept out of the repo.
6. **Ship** — Dockerized services on AWS EC2 (or Railway), frontend on Vercel, auth and storage on Supabase or S3, CI on GitHub Actions.
7. **Document & hand over** — A README and architecture decision records that let the next engineer run, change, and deploy the system without me.

---

### Featured project

#### [Exergy Intelligence](https://exergyintelligence.tech) — Cost intelligence for moving goods in volatile markets
A multi-tenant platform that prices fuel, haulage corridors, and landed import cost for Nigerian fleets and importers — connecting diesel prices by state, naira exchange rates, and freight quotes into one cost picture. Every figure is labelled **observed**, **estimated**, or **forecast** with a confidence score, so a prediction is never shown as a fact.

- **What it does:**
  - **Corridor costing** — a Lagos → Kano trip is priced leg by leg at the diesel price of each state where the truck refuels, then driver, tolls, and maintenance are added.
  - **Landed cost** — CIF, duty, surcharges, VAT, port, clearing, and inland haulage, with what-if shocks to the naira, diesel, or freight that show duty and VAT compounding through to margin.
  - **Market data ingestion** — scheduled FX ingestion plus CSV and manual fuel/freight intake, all through one normalise → validate → stamp provenance → dedupe pipeline.
  - Fuel price maps, receipt auditing, risk scoring, and a public, coarsened corridor sandbox.
- **Architecture:** pnpm monorepo — **Next.js 16** frontend, a **Fastify** modular monolith (21 modules), and a **BullMQ** worker, over **MongoDB** and **Redis**, with five shared TypeScript packages for types, config, validation, and market-data rules.
- **Engineering decisions worth noting:**
  - **Tenant isolation by construction** — repository base classes make an unscoped query unreachable rather than discouraged; cross-tenant access returns 404, not 403.
  - **Deterministic cost engine** — calculators are pure functions with no I/O, so every historical calculation is reproducible and unit-testable without fixtures.
  - **Rounding done once** — full precision through every intermediate, rounded only at the money boundary. This caught a systematic overstatement in the product spec itself, now locked in by tests.
  - **API-first** — the UI consumes the same documented REST API (OpenAPI) that customers can wire into their ERP or TMS.
- **Quality:** 250+ tests (unit, integration, e2e) run in GitHub Actions against real MongoDB and Redis services, behind format, lint, and typecheck gates. 12 architecture decision records.

---

### Selected work

#### [`ckd`](https://github.com/elshaddaioheha/ckd) — ML decision-support system for Chronic Kidney Disease screening · [Live demo](https://ckd-ruby.vercel.app)
A full-stack AI application that estimates CKD risk from 20 standard clinical and laboratory inputs.

- **Approach:** Structured patient data is converted into a clinical narrative and classified by a **fine-tuned DistilBERT** model, with a rule-based reasoning layer that flags findings (elevated creatinine, hypertension, anaemia) and explains *why* the model reached its result.
- **Frontend:** Next.js 15 (App Router), React 19, Tailwind, React Hook Form + Zod; print stylesheet for clean PDF reports; built-in µmol/L ↔ mg/dL creatinine conversion for West African lab standards.
- **Backend:** FastAPI + PyTorch + Hugging Face Transformers, Dockerized on Railway, model hosted on the Hugging Face Hub.
- **Engineering wins:**
  - Cut the Docker image from **4 GB+ to under 1 GB** by switching to CPU-only PyTorch wheels.
  - Fixed failing deploys by moving the ~260 MB model download to a background thread, so `/health` responds instantly and reports load status.
  - Resolved production CORS failures caused by trailing-slash URL mismatches between Vercel and Railway.
- **Responsible scope:** The UI states clearly that the model is not validated for regional drivers such as sickle cell nephropathy or herbal nephrotoxicity.

#### [`talllie-reservation-api`](https://github.com/elshaddaioheha/talllie-reservation-api) — Concurrency-safe booking engine
A restaurant booking API designed around the failure case first: two users tapping *Reserve* on the same table at the same moment. Atomic PostgreSQL transactions, a best-fit seating algorithm, and no double-bookings under load.

#### [`SwenAutos`](https://github.com/elshaddaioheha/SwenAutos) — Auto-parts marketplace
End-to-end TypeScript marketplace — listings, inventory, and checkout — with a clean separation between domain and transport layers.

#### [`node-template-oheha`](https://github.com/elshaddaioheha/node-template-oheha) — Opinionated Node.js starter
A strict take on separation of concerns: thin controllers, pure services. Ships with a small validation DSL for payload schemas, Husky + Commitlint, and structured error handling.


---

### Activity

<p align="left">
  <img height="170" alt="GitHub streak" src="https://streak-stats.demolab.com?user=elshaddaioheha&hide_border=true&background=0D1117&stroke=0D1117&ring=FFFFFF&fire=FFFFFF&currStreakNum=FFFFFF&sideNums=FFFFFF&currStreakLabel=FFFFFF&sideLabels=8B949E&dates=8B949E" />
  <img height="170" alt="GitHub stats" src="https://github-readme-stats.vercel.app/api?username=elshaddaioheha&show_icons=true&hide_border=true&hide_title=false&bg_color=0D1117&title_color=FFFFFF&icon_color=8B949E&text_color=C9D1D9&count_private=true&include_all_commits=true" />
</p>

<img alt="Commit activity" src="https://github-readme-activity-graph.vercel.app/graph?username=elshaddaioheha&bg_color=0D1117&color=FFFFFF&line=FFFFFF&point=8B949E&area=true&area_color=8B949E&hide_border=true&custom_title=Commits%20over%20time" />

<!-- Animated commit-grid render. Generated by .github/workflows/snake.yml -->
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/elshaddaioheha/elshaddaioheha/output/github-contribution-grid-snake-dark.svg" />
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/elshaddaioheha/elshaddaioheha/output/github-contribution-grid-snake.svg" />
  <img alt="Snake eating my contributions" src="https://raw.githubusercontent.com/elshaddaioheha/elshaddaioheha/output/github-contribution-grid-snake.svg" />
</picture>

---

### Working with me

The fit is good when:

- The backend is load-bearing — concurrency, money, or data correctness actually matter.
- You want one engineer who can scope the work, ship it, and document it for whoever comes next.
- You prefer weekly check-ins and vertical slices over a black-box "it'll be done in a month."

The fit is not good for brochure sites, no-code work, or projects without a real product owner.

For a scoped quote, send the rough shape of the problem to **[elshaddaioheha@gmail.com](mailto:elshaddaioheha@gmail.com)** — happy to do a 30-minute call before either side commits.
