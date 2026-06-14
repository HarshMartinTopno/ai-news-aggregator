# AI News Aggregator

A Python pipeline that fetches, processes, and surfaces the top AI news stories from the last N hours — runnable locally or inside Docker.

---

## What It Does

The pipeline is invoked via `main.py` and delegates all logic to `app/daily_runner.py`. On each run it:

1. Fetches articles from RSS/Atom feeds and web sources (via `feedparser` + `requests` + `BeautifulSoup4`)
2. Optionally pulls YouTube video transcripts (via `youtube-transcript-api`)
3. Parses and normalises content using `docling` and `markdownify`
4. Ranks and deduplicates stories, returning the top N articles from the last `hours` window
5. Persists results to a PostgreSQL database (via `SQLAlchemy` + `psycopg2`)
6. Uses the OpenAI API to summarise or enrich articles

Exit code `0` = success, `1` = failure — making it safe to wire into cron or CI pipelines.

---

## Project Structure

```
ai-news-aggregator/
├── app/                    # Core application logic
│   └── daily_runner.py     # Orchestrates the full pipeline
├── docker/                 # Docker configuration files
├── main.py                 # Entry point; accepts CLI args
├── pyproject.toml          # Dependencies and project metadata
├── uv.lock                 # Locked dependency graph (uv)
├── .python-version         # Pins Python 3.12
└── .gitignore
```

---

## Requirements

- Python 3.12+
- PostgreSQL (running locally or via Docker)
- An OpenAI API key
- [`uv`](https://github.com/astral-sh/uv) (recommended) **or** `pip`

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/HarshMartinTopno/ai-news-aggregator.git
cd ai-news-aggregator
```

### 2. Install dependencies

With `uv` (recommended — respects `uv.lock`):

```bash
uv sync
```

Or with pip:

```bash
pip install .
```

### 3. Configure environment variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key
DATABASE_URL=postgresql://user:password@localhost:5432/ai_news
```

Adjust `DATABASE_URL` to match your PostgreSQL setup.

---

## Running

### Basic run (last 24 hours, top 10 articles)

```bash
python main.py
```

### Custom window and article count

```bash
python main.py <hours> <top_n>
```

**Examples:**

```bash
python main.py 12 5      # Last 12 hours, top 5 articles
python main.py 48 20     # Last 48 hours, top 20 articles
```

### With Docker

```bash
docker compose up --build
```

_(Refer to the `docker/` directory for `Dockerfile` and `compose` configuration.)_

---

## Dependencies

| Package | Purpose |
|---|---|
| `feedparser` | RSS/Atom feed ingestion |
| `requests` + `beautifulsoup4` | Web scraping and HTML parsing |
| `docling` | Document parsing and extraction |
| `markdownify` | HTML-to-Markdown conversion |
| `youtube-transcript-api` | YouTube transcript extraction |
| `openai` | AI-powered summarisation |
| `sqlalchemy` + `psycopg2-binary` | PostgreSQL ORM and driver |
| `pydantic` | Data validation and schemas |
| `python-dotenv` | Environment variable loading |
| `markdown` | Markdown rendering |

---

## Exit Codes

| Code | Meaning |
|---|---|
| `0` | Pipeline ran successfully |
| `1` | Pipeline encountered an error |

---

## License

Not specified. Contact the repository owner for usage terms.
