# Tech Stack — Social Saver

## Backend
| Technology | Version | Purpose |
|---|---|---|
| **Python** | 3.11.9 | Core language |
| **FastAPI** | 0.115.0 | Web framework — routes, request handling |
| **Uvicorn** | 0.30.0 | ASGI server |
| **Jinja2** | 3.1.4 | Server-side HTML templating |
| **python-multipart** | 0.0.9 | Form data parsing |
| **itsdangerous** | 2.2.0 | Session cookie signing |
| **bcrypt** | 4.2.0 | Password hashing |
| **python-dotenv** | 1.0.1 | Environment variable loading |

## Database
| Technology | Purpose |
|---|---|
| **SQLite** | Local development database |
| **psycopg2-binary** 2.9.9 | PostgreSQL driver (production) |

## AI & Scraping
| Technology | Version | Purpose |
|---|---|---|
| **Google Gemini API** (`google-generativeai`) | 0.8.0 | AI categorisation, summarisation, tag generation — tries `gemini-2.0-flash` → `gemini-1.5-flash` → `gemini-2.0-flash-lite` in order |
| **httpx** | 0.27.0 | Async HTTP client for scraping |
| **BeautifulSoup4** | 4.12.3 | HTML parsing (OG metadata extraction) |
| **Instagram oEmbed API** | — | Scrape Instagram post captions + thumbnails |
| **Twitter/X oEmbed API** | — | Scrape tweet text (`publish.twitter.com/oembed`) |
| **facebookexternalhit/1.1 UA** | — | Fallback scraping — forces sites to serve OG metadata |
| **YouTube OG metadata** | — | Scrape YouTube video title + thumbnail via og:title/og:image |
| **Groq API** (`llama-3.1-8b-instant`) | — | Secondary AI fallback — OpenAI-compatible endpoint (`https://api.groq.com/openai/v1`), used if all Gemini models fail |
| **Keyword fallback** | — | Tertiary AI fallback — pure Python keyword scoring, no external API, guarantees a category even if both Gemini and Groq are unavailable |
| **aiofiles** | 24.1.0 | Async file I/O |

## Messaging
| Technology | Version | Purpose |
|---|---|---|
| **Twilio** | 9.3.0 | WhatsApp integration via the Twilio Sandbox (`whatsapp:+14155238886`) |
| **TwiML `MessagingResponse`** | — | Builds XML replies sent back through Twilio's webhook response |
| **Webhook endpoint** `POST /webhook/whatsapp` | — | Receives `From` (WhatsApp number) + `Body` (message text) form fields from Twilio; parses URL, scrapes platform, runs AI or MCQ, saves link, replies |
| **MCQ category flow** | — | When scraping yields no usable text, sends a numbered multiple-choice question (6 platform-specific options) to the user; supports 1 retry before dropping |

## Frontend
| Technology | Purpose |
|---|---|
| **HTML + Jinja2 templates** | Server-rendered pages (login, register, dashboard, chat) |
| **Vanilla CSS** (`style.css`) | Airbnb-style light UI, responsive, custom variables |
| **Vanilla JavaScript** | Dashboard interactions, chat UI, pagination, search |
| **GSAP 3.12.5 + ScrollTrigger** | Scroll-linked search bar shrink animation |
| **Lucide Icons** (CDN) | Icon library |
| **Visual Viewport API** | Mobile keyboard-aware chat layout (WhatsApp-style) |

## Architecture
- **Monolithic** — single FastAPI process serves HTML, static files, API routes, and webhook
- **Session auth** — signed cookie (`itsdangerous`) with server-side user lookup
- **Hybrid DB** — SQLite locally, PostgreSQL in production (auto-detected via `DATABASE_URL` env var)
- **In-memory session store** — pending links and MCQ state stored in Python dict (`session_store.py`); resets on restart (acceptable for hackathon demo)
- **3-tier AI fallback** — Gemini (3 models) → Groq (`llama-3.1-8b-instant`) → keyword scoring; ensures a category is always assigned
- **No frontend build step** — pure server-side rendering, no React/Vue/Node
