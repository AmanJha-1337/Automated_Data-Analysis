# Automated Data Analysis

A backend service that takes a CSV/Excel file, cleans and analyzes it, and returns an AI-generated summary of the data — without making you wait for the processing to finish.

## How it works

1. **Upload** a CSV/Excel file to the API.
2. The API immediately queues the job (using **Celery + Redis**) and returns a `task_id` — no waiting around.
3. A **background worker** cleans the data (missing values, duplicates, type fixes) and calculates statistics (mean, std dev, correlations, distributions) using **Pandas/NumPy**.
4. Those statistics are sent to an **LLM (Groq / OpenAI-compatible API)**, which generates a plain-English summary of trends and patterns.
5. You **poll `/status/{task_id}`** to check progress, then hit **`/results/{task_id}`** to get the final report once it's ready.

## Tech Stack

- **FastAPI** – REST API
- **Celery + Redis** – background job processing
- **Pandas / NumPy / openpyxl** – data cleaning & stats
- **Groq / OpenAI API** – AI-generated insights

## Quick Start

```bash
git clone https://github.com/AmanJha-1337/Automated_Data-Analysis.git
cd Automated_Data-Analysis
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r autoAnalysis/requirements.txt
cp .env.example .env   # add your GROQ_API_KEY and REDIS_URL

# Start Redis
redis-server

# Start the worker
celery -A autoAnalysis.worker worker --loglevel=info

# Start the API
uvicorn autoAnalysis.main:app --reload
```

Visit `http://localhost:8000/docs` for the interactive API docs.

## API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/upload` | POST | Upload a file for analysis |
| `/status/{task_id}` | GET | Check job progress |
| `/results/{task_id}` | GET | Get the final report |
| `/health` | GET | Health check |

## License

MIT
