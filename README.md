# AI Research Agent

A command-line agent that takes a topic, researches it on the web, and
writes a clean Markdown summary with sources — no manual copy-pasting
from search results.

Runs entirely free and local: **Ollama** (local LLM) for planning and
writing, **DuckDuckGo** for search. No API key needed.

## What it does

1. You give it a topic.
2. It plans 2-3 distinct search queries to cover the topic from different
angles.
3. It runs those searches (DuckDuckGo, no key required).
4. It synthesizes everything into a structured report: overview, key
points, and a source list -grounded only in what it actually found.
5. Saves the report as a `.md` file.

## Why I built this

Working as an AI evaluation intern, I spend a lot of time judging whether
agent trajectories are grounded, complete, and non-hallucinated. This
project is the "build" side of that — an agent that has to plan its own
searches and produce a summary that's traceable back to real sources.

## Demo

<!-- Add a screenshot or terminal recording here, e.g.
!\\\[demo](demo.png)

\*\*Example run:\*\*

```
$ python agent\\\_free.py "AI job market in India 2026"
Planning searches for: AI job market in India 2026
Search queries: \\\['ai job market india 2026 trends statistics', ...]
  Searching: ai job market india 2026 trends statistics
  Searching: ai employment outlook india 2026 growth projections
  Searching: artificial intelligence job prospects indian economy
Synthesizing summary...

Done. Sources found: 12
Saved to: report\\\_20260708\\\_170152.md
```

## Setup

```bash
# 1. Install Ollama: https://ollama.com
ollama pull llama3.1:8b
ollama serve   # keep this running in a separate terminal (or it may already be running)

# 2. Clone and install
git clone https://github.com/Aaditi-kumari/ai-research-agent.git
cd ai-research-agent
python -m venv venv
venv\\\\Scripts\\\\activate      # Windows
pip install -r requirements\\\_free.txt

# 3. Run
python agent\\\_free.py "your topic here"
python agent\\\_free.py "your topic here" --output my\\\_report.md --model llama3.1:8b
```

## How it works (architecture)

Local models don't reliably self-direct tool use the way hosted models
with native tool-calling do, so the agent is broken into explicit steps:

1. Ask the local model to plan 2-3 distinct search queries for the topic.
2. Run each query through DuckDuckGo (`ddgs`, free, no API key).
3. Feed all collected snippets back to the model to synthesize a final
report, instructed to use only what was actually found — not invent
facts.

Every source URL surfaced during search is tracked and appended to the
report, so the summary is traceable back to where it came from.

## Tech stack

\* Python
\* Ollama (local LLM — `llama3.1:8b`)
\* DuckDuckGo search (`ddgs`)
\* `requests` for the Ollama API calls

## Possible extensions

\* Swap Markdown output for a PDF report
\* Add a `--depth` flag to control how many searches it's allowed to run
\* Wrap it in a small Streamlit UI (this is basically Phase 2 project #3)
\* Swap Ollama for the Claude API (hosted `web\\\_search` tool) as a paid,
more autonomous alternative




