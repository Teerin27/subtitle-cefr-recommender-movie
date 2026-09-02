# CEFR of Movie 

> Movie recommendations matched to your English proficiency level.

[live demo](https://...) · [case study](docs/CASE_STUDY.md) · [demo video](https://...)

![demo gif](docs/demo.gif)

---

## The Problem

Language learners are told to "watch movies in English" — but no one tells you
*which* movies. Pick something too hard and you quit; too easy and you learn nothing.

Existing recommenders optimize for what you'll *enjoy*. This one optimizes for what
you'll enjoy **at a level you can actually learn from** — Krashen's *i+1*: slightly
above your current level, not at it.

## How It Works

[architecture diagram]

Three stages:

1. **Difficulty Analysis** — parses .srt subtitles into 4 axes:
   vocabulary, grammar, speech rate, and context density → CEFR level (A2–C2)
2. **Candidate Generation** — EASE^R collaborative filtering on MovieLens 25M
3. **Level-Aware Re-ranking** — combines relevance × level-fit, with an
   asymmetric penalty (too hard hurts more than too easy)

## Results

| Metric | Score |
|---|---|
| Difficulty model — Spearman ρ | 0.xx |
| Difficulty model — QWK | 0.xx |
| Recommender — NDCG@10 | 0.xx |
| Level-fit rate@10 | xx% |

**Sanity check:** Paddington (A2) < Inception (B2) < Margin Call (C1) 

## Why This Level? (Explainability)

Every recommendation shows *why*. [screenshot ของ radar chart]

## Tech Stack
[ตาราง — ดูข้อ 4]

## Getting Started
[docker compose up + env setup]

## Project Structure
[tree]

## Known Limitations
- Subtitle coverage: xx% of MovieLens titles
- Readability formulas are built for written text, not dialogue
- Visually-dense films (e.g. 2001) score "easy" but aren't
- MovieLens users ≠ language learners

## Data & Licensing
Subtitle text is never stored or served — only derived statistical features.

## Roadmap
## License

## Tech Stack

### ML & Data
| Tool | Why |
|---|---|
| Python 3.11+ | Same language across ML and API — critical for a solo build |
| Polars | 5–10× faster than pandas on 10k+ subtitle files; lazy eval keeps RAM low |
| spaCy | Batch NLP + DependencyMatcher for detecting EGP grammar patterns |
| LightGBM | Monotonic constraints for ordinal targets; SHAP makes it explainable |
| scikit-learn | Preprocessing, PCA composite index, evaluation |
| MLflow | Experiment tracking + model registry |
| DVC | Versioning datasets and model artifacts (too large for git) |

### Backend
| Tool | Why |
|---|---|
| FastAPI | Async, auto OpenAPI docs, Pydantic validation, same runtime as ML |
| PostgreSQL + pgvector | Relational + vector search in one database — no separate vector DB |
| SQLAlchemy + Alembic | ORM and schema migrations |
| Redis | Caches precomputed candidates; rate limiting |
| Pydantic v2 | Shared schema contract between model output and API |

### Frontend
| Tool | Why |
|---|---|
| Next.js 15 (App Router) | SSR for SEO on movie pages; one-click Vercel deploy |
| TypeScript | Type safety across API boundary |
| Tailwind CSS + shadcn/ui | Ship a polished UI fast without designing from scratch |
| TanStack Query | Server state, caching, optimistic feedback updates |
| Recharts | Radar chart for the 4-axis difficulty breakdown |

### Infrastructure
| Tool | Why |
|---|---|
| Docker + Compose | Reproducible local dev, matches production |
| Vercel | Frontend hosting |
| Railway / Fly.io | Containerized FastAPI |
| Neon / Supabase | Managed Postgres with pgvector |
| Cloudflare R2 | Model artifacts and parquet — zero egress fees |
| GitHub Actions | CI (lint, test, type-check) + weekly retraining cron |
| Sentry | Error tracking |
| Ruff + mypy | Linting and static typing |

### Data Sources
| Source | Use |
|---|---|
| MovieLens 25M | Ratings, movie metadata, ID mapping |
| OPUS OpenSubtitles | Subtitle corpus |
| TMDB API | Posters, overviews, runtime |
| English Vocabulary Profile | CEFR-tagged vocabulary |
| English Grammar Profile | CEFR-tagged grammar structures |
| COCA / BNC frequency lists | Word frequency bands |

### Deliberately Not Used
Airflow, Kubernetes, Kafka, and a separate vector database. At this scale a weekly
cron job and Postgres do the same work with a fraction of the operational cost —
a tradeoff worth making when one person maintains everything.
