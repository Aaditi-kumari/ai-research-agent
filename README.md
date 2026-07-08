# AI Research Agent

A command-line agent that takes a topic, researches it on the web using
Claude's search tool, and writes a clean Markdown summary with sources —
no manual copy-pasting from search results.

## What it does

1. You give it a topic.
2. It decides how many web searches it needs (usually 2-4) to cover the
   topic from different angles.
3. It synthesizes everything into a structured report: overview, key
   points, and a source list.
4. Saves the report as a `.md` file.

## Why I built this

Working as an AI evaluation intern, I spend a lot of time judging whether
agent trajectories are grounded, complete, and non-hallucinated. This
project is the "build" side of that — an agent that has to plan its own
searches, decide when it has enough information, and cite what it used.

## Demo

<!-- Add a screenshot or terminal recording here, e.g.
![demo](demo.png)
-->

**Example run:**
```
$ python agent.py "adoption of RAG systems in enterprise 2026"
Researching: adoption of RAG systems in enterprise 2026

Done in 3 research turn(s).
Sources found: 7
Saved to: report_20260708_1432.md
```

## Two versions

| | `agent.py` | `agent_free.py` |
|---|---|---|
| LLM | Claude API (hosted) | Ollama (local, e.g. `llama3.1:8b`) |
| Search | Claude's hosted web search tool | DuckDuckGo (free, no key) |
| Cost | Paid (API usage) | Free |
| Setup | Needs `ANTHROPIC_API_KEY` | Needs Ollama running locally |

### Setup — paid version (`agent.py`)

```bash
git clone https://github.com/YOUR_USERNAME/ai-research-agent.git
cd ai-research-agent
pip install -r requirements.txt
export ANTHROPIC_API_KEY="your-key-here"
python agent.py "your topic here"
```

### Setup — free version (`agent_free.py`)

```bash
# 1. Install Ollama: https://ollama.com
ollama pull llama3.1:8b
ollama serve   # keep this running in a separate terminal

# 2. Install Python deps
pip install -r requirements_free.txt

# 3. Run
python agent_free.py "your topic here"
python agent_free.py "your topic here" --output my_report.md --model llama3.1:8b
```

## How it works (architecture)

**Paid version (`agent.py`):**
- Uses Claude's server-side `web_search` tool — the model itself decides
  what to search for and when to stop.
- Agent loop keeps calling Claude until it stops requesting tool use,
  meaning it has decided it's done researching.

**Free version (`agent_free.py`):**
- Local models don't reliably self-direct tool use, so this version uses
  explicit steps: (1) ask the local model to plan 2-3 search queries,
  (2) run each through DuckDuckGo, (3) feed all results back to the model
  to synthesize the final report.
- Trade-off: less autonomous, but same output structure, zero cost.

Both versions track every source URL surfaced during search and append it
to the report, so every summary is traceable.

## Tech stack

- Python
- `agent.py`: Anthropic API (Claude Sonnet + hosted web search tool)
- `agent_free.py`: Ollama (local LLM) + DuckDuckGo search (`ddgs`)

## Possible extensions

- Swap Markdown output for a PDF report
- Add a `--depth` flag to control how many searches it's allowed to run
- Wrap it in a small Streamlit UI (this is basically Phase 2 project #3)
