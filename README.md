# Sahayaa — AI-Based Hyperlocal Helper Platform

> Connecting working couples, elderly people, and new parents with trusted local helpers for daily chores, instant tasks, and long-term care — powered by AI smart matching and voice booking.



## The Problem

- Both partners in a household are working — no time for daily chores
- Elderly people living alone need **reliable, recurring** help they can trust
- New parents are overwhelmed and need nannies or post-delivery care
- Existing platforms like Urban Company focus on **one-time services** — not ongoing relationships


## Our Solution

**Sahayaa** is an AI-powered website that connects people with verified local helpers for:

- **Instant help** — task done within 1–2 hours
- **Daily helpers** — same helper every day on a monthly basis (cooks, maids, nannies)

Unlike gig-economy platforms, Sahayaa is built around **continuity and trust** — the same helper visits the same family, builds familiarity, and the AI ensures the match is right from day one.

## Key Features

### AI Smart Matching
Matches users with helpers based on:
- Proximity (same locality / within X km)
- Time availability and schedule alignment
- Budget range
- Language preference (Kannada, Hindi, English)
- Service type and skill tags

### Voice-Based Booking
- Elderly users and helpers can speak instead of type
- Built using the **Web Speech API** (browser-native, no library needed)
- Supports Kannada and English

### Trust & Safety
- Aadhaar-based identity verification (DigiLocker / Signzy API)
- Police verification badge for helpers
- AI-moderated ratings to flag fake reviews
- SOS / emergency contact button for vulnerable users

### Flexible Scheduling
- Instant booking (available now → helper arrives within the hour)
- Monthly recurring schedule (fixed helper, fixed time slot)

### In-App Communication
- Chat between user and helper
- SMS and WhatsApp notifications via Twilio
- Browser push notifications (Firebase)

### Payments
- Razorpay integration (UPI, cards, wallets)
- Weekly automated payouts to helpers


## Booking Types

### Instant Help
- User requests help → AI matches the nearest available helper → helper accepts → arrives within 1–2 hours
- Ideal for: cleaning, grocery runs, ad-hoc cooking

### Daily Helper (Monthly)
- User sets a recurring schedule → AI finds a helper who matches all criteria → same helper visits every day
- Ideal for: morning cook, daily maid, part-time nanny
- Helper slot is reserved exclusively for that household

## Tech Stack

| Layer | Technology | Purpose |
|---    |---         |---      |
| Frontend | Next.js 14 (React) + Tailwind CSS | PWA, works on all browsers and mobile |
| Backend | **Python 3.11 + FastAPI** | High-performance async REST API |
| ORM | **SQLAlchemy 2.0 + Alembic** | Database models and migrations |
| Database | PostgreSQL 15 + PostGIS | Relational data + location-based queries |
| Cache | Redis + **aioredis** | Session management, real-time availability |
| Task Queue | **Celery + Redis** | Background jobs, scheduled notifications |
| AI Matching | **Groq + llama3 + scikit-learn** | Smart helper-user matching |
| Voice | Web Speech API (browser-native) | Voice input for booking |
| Maps | Google Maps API | Geocoding, distance matrix, helper location |
| Payments | Razorpay | UPI, cards, wallet, payouts |
| Notifications | Twilio (SMS/WhatsApp) + Firebase | Booking alerts, reminders |
| Identity Check | Aadhaar / Signzy API | Helper background verification |
| API Docs | **Swagger UI (auto via FastAPI)** | Live docs at `/docs` |
| Hosting | Vercel (frontend) + Railway / AWS (backend) | Scalable deployment |


## System Architecture

┌──────────────────────────────────────────────────┐
│                User / Helper Browser              │
│           (PWA — works on PC + Mobile)            │
└─────────────────┬────────────────────────────────┘
                  │ HTTPS
┌─────────────────▼────────────────────────────────┐
│               Next.js Frontend                    │
│    Home │ Browse │ Book │ Dashboard │ Chat        │
└─────────────────┬────────────────────────────────┘
                  │ REST / WebSocket
┌─────────────────▼────────────────────────────────┐
│          Python FastAPI Backend                   │
│  Auth │ Booking │ Matching │ Payments │ Chat      │
└──┬──────────┬──────────┬───────────┬─────────────┘
   │          │          │           │
┌──▼───┐ ┌───▼───┐ ┌────▼────┐ ┌───▼──────────┐
│  PG  │ │ Redis │ │ Claude  │ │  Razorpay    │
│  DB  │ │ Cache │ │   API   │ │  Twilio      │
└──────┘ └───────┘ └─────────┘ └──────────────┘
              │
       ┌──────▼──────┐
       │   Celery    │
       │ Task Queue  │
       └─────────────┘

## Project Structure

```
Sahayaa/
│
├── frontend/                        # Next.js PWA
│   ├── app/
│   │   ├── page.tsx                 # Landing page
│   │   ├── browse/                  # Browse helpers
│   │   ├── book/                    # Booking flow
│   │   ├── dashboard/               # User dashboard
│   │   └── helper/                  # Helper portal
│   ├── components/
│   │   ├── HelperCard.tsx
│   │   ├── VoiceSearch.tsx          # Web Speech API component
│   │   ├── BookingModal.tsx
│   │   └── MapView.tsx
│   ├── package.json
│   └── next.config.js
│
├── backend/                         # Python FastAPI application
│   ├── main.py                      # FastAPI app entry point
│   ├── requirements.txt             # Python dependencies
│   │
│   ├── api/                         # Route handlers
│   │   ├── __init__.py
│   │   ├── auth.py                  # Register, login, JWT
│   │   ├── helpers.py               # Helper search, profiles
│   │   ├── bookings.py              # Instant + monthly bookings
│   │   ├── payments.py              # Razorpay order + verify
│   │   ├── reviews.py               # Ratings and reviews
│   │   └── notifications.py         # Trigger SMS / push
│   │
│   ├── models/                      # SQLAlchemy ORM models
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── helper.py
│   │   ├── booking.py
│   │   ├── payment.py
│   │   └── review.py
│   │
│   ├── schemas/                     # Pydantic request/response schemas
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── helper.py
│   │   ├── booking.py
│   │   └── payment.py
│   │
│   ├── services/                    # Business logic layer
│   │   ├── __init__.py
│   │   ├── matching_engine.py       # AI scoring algorithm
│   │   ├── voice_parser.py          # Parse voice booking input
│   │   ├── payment_service.py       # Razorpay integration
│   │   ├── notification_service.py  # Twilio + Firebase
│   │   └── verification_service.py  # Aadhaar / Signzy KYC
│   │
│   ├── tasks/                       # Celery background tasks
│   │   ├── __init__.py
│   │   ├── celery_app.py
│   │   ├── reminder_tasks.py        # Scheduled booking reminders
│   │   └── payout_tasks.py          # Weekly helper payouts
│   │
│   ├── core/                        # App config and utilities
│   │   ├── __init__.py
│   │   ├── config.py                # Settings via pydantic-settings
│   │   ├── database.py              # SQLAlchemy engine + session
│   │   ├── redis_client.py          # aioredis connection
│   │   ├── security.py              # JWT + password hashing
│   │   └── dependencies.py          # FastAPI dependency injection
│   │
│   ├── migrations/                  # Alembic DB migrations
│   │   ├── env.py
│   │   └── versions/
│   │
│   └── tests/                       # Pytest test suite
│       ├── __init__.py
│       ├── test_auth.py
│       ├── test_bookings.py
│       ├── test_matching.py
│       └── conftest.py
│
├── ai/                              # Standalone AI scripts / notebooks
│   ├── match_helper.py              # Scoring logic (also used by backend)
│   ├── train_embeddings.py          # Helper embedding model (Phase 2)
│   └── notebooks/
│       └── matching_exploration.ipynb
│
├── docs/                            # Additional documentation
│   └── api_reference.md
│
├── .env.example                     # Environment variable template
├── .gitignore
├── docker-compose.yml               # Local dev: PG + Redis + API
├── Dockerfile                       # Backend container
└── README.md
```

## Getting Started

### Prerequisites

- Python 3.11+
- Node.js v18+ (for frontend)
- PostgreSQL 15+ with PostGIS extension
- Redis 7+
- A Razorpay account
- Google Maps API key
- Anthropic API key

## API Overview

All routes are prefixed with `/api/v1`. Full interactive documentation is auto-generated by FastAPI at `/docs`.

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/auth/register` | Register a new user or helper |
| `POST` | `/api/v1/auth/login` | Login and receive JWT token |
| `GET` | `/api/v1/helpers/search` | Search helpers by location + filters |
| `GET` | `/api/v1/helpers/{id}` | Get a helper's full profile |
| `POST` | `/api/v1/bookings/instant` | Create an instant booking |
| `POST` | `/api/v1/bookings/monthly` | Create a monthly recurring booking |
| `GET` | `/api/v1/bookings/user/{id}` | Get all bookings for a user |
| `PATCH` | `/api/v1/bookings/{id}/status` | Update booking status |
| `POST` | `/api/v1/payments/create-order` | Initiate a Razorpay order |
| `POST` | `/api/v1/payments/verify` | Verify payment signature |
| `POST` | `/api/v1/reviews` | Submit a post-service review |
| `GET` | `/api/v1/reviews/helper/{id}` | Get all reviews for a helper |

---

## AI Engine

### Matching Algorithm (`backend/services/matching_engine.py`)

The engine scores every available helper against the user's request using a weighted formula:

```python
def calculate_match_score(helper: Helper, request: BookingRequest) -> float:
    location_score   = score_proximity(helper.lat, helper.lng,
                                       request.lat, request.lng)   # 0.0 – 1.0
    availability     = score_availability(helper.schedule,
                                          request.time_slot)        # 0.0 – 1.0
    budget_fit       = score_budget(helper.rate, request.budget)    # 0.0 – 1.0
    language_match   = score_language(helper.languages,
                                      request.preferred_language)   # 0.0 – 1.0
    rating_score     = helper.avg_rating / 5.0                      # 0.0 – 1.0

    return (
        location_score * 0.35 +
        availability   * 0.25 +
        budget_fit     * 0.20 +
        language_match * 0.10 +
        rating_score   * 0.10
    )
```

The top 3–5 scoring helpers are returned to the user. If no helper scores above the minimum threshold, the search radius is automatically widened.



### Python Dependencies (`backend/requirements.txt`)

```
fastapi==0.111.0
uvicorn[standard]==0.29.0
sqlalchemy==2.0.30
alembic==1.13.1
psycopg2-binary==2.9.9
asyncpg==0.29.0
pydantic==2.7.1
pydantic-settings==2.2.1
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.9
aioredis==2.0.1
celery==5.3.6
anthropic==0.28.0
scikit-learn==1.4.2
razorpay==1.4.1
twilio==9.1.0
firebase-admin==6.5.0
httpx==0.27.0
pytest==8.2.0
pytest-asyncio==0.23.6
```

## Trust & Safety

- **Identity verification** — All helpers complete Aadhaar-based KYC via Signzy before going live
- **Police verification** — Optional but displayed as a badge; significantly increases booking conversion
- **AI review moderation** — Flags suspicious patterns such as burst 5-star reviews or repeated reviewer accounts
- **SOS button** — One-tap emergency alert sends the user's GPS location to a pre-set emergency contact
- **Safe payment** — Funds are held and released to helpers only after the user confirms service completion

## Roadmap

### Phase 1 — MVP (Weeks 1–10)
- [x] User and helper registration + profiles
- [x] Manual search and booking (rule-based, no AI yet)
- [x] WhatsApp booking confirmations via Twilio
- [x] Basic star ratings
- [ ] Launch in 1 Bengaluru locality with 20–30 onboarded helpers

### Phase 2 — AI Live (Weeks 11–20)
- [ ] AI smart matching engine (Python scoring + Claude API)
- [ ] Voice-based booking (Web Speech API + Claude parser)
- [ ] In-app chat (WebSocket via FastAPI)
- [ ] Razorpay payments + weekly helper payouts via Celery
- [ ] Background verification integration (Signzy)

### Phase 3 — Scale (Months 6–12)
- [ ] Multi-city rollout
- [ ] Subscription plans (monthly helper packages)
- [ ] Helper training and certification badges
- [ ] Mobile app (React Native)
- [ ] B2B offering for apartment complexes and co-living spaces