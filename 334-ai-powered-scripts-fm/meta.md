# Session 334 — Build AI-powered scripts with the fm CLI and Python SDK

- **URL:** https://developer.apple.com/videos/play/wwdc2026/334/
- **Duration:** 17m
- **Speakers:** Eric Gourlaouen (Foundation Models Framework team)

## Description

Two new ways to reach Apple Foundation Models on macOS 27 outside of Swift: the
`fm` command line tool (pre-installed, for terminal prompting and shell automation)
and the Foundation Models SDK for Python (for ML engineers and data scientists who
live in Python). The talk walks the `fm` basics, builds a real file-triage
automation script, mirrors the Swift API in Python (sessions, tool calling, guided
generation), and closes with a Jupyter/Pandas/matplotlib evaluation pipeline that
scores three prompt variants with a server judge model.

## Key topics

- `fm` CLI — pre-installed with macOS 27; `fm respond`, `fm chat`, `fm schema`; on-device default + Private Cloud Compute via `--model pcc`
- `fm chat` interactive REPL with `/model` (switch to PCC) and `/save` (resume sessions later) commands
- `fm respond` for inline/scriptable responses; `--instructions`, `--image`, `--model`, `--schema`, `--help` options
- `fm schema object` to build a JSON generation schema; piping structured JSON output through `jq` in shell scripts
- File-triage automation demo — model sorts messy draft vs. final filenames, returns structured JSON, script routes to backup/archive
- Foundation Models SDK for Python — `pip install apple_fm_sdk`; requires Python 3.10+, Xcode, Apple Silicon
- Python API mirrors Swift — `fm.LanguageModelSession`, `session.respond`, text + image inputs, streaming, tool calling, guided generation
- `fm.Tool` subclassing + `fm.generable` decorator + `fm.guide` for structured output into typed objects
- Evaluation pipeline in Python — Jupyter + Pandas + matplotlib; generate outputs with on-device model, score with server judge model, chart results
- Judge criteria: generation errors, excess items, missing items, hallucinated items — used to compare a minimal vs. descriptive vs. comprehensive prompt

## Related sessions to fetch (referenced in this talk)

- [ ] What's new in the Foundation Models framework (Session 241 — the parent/overview)
- [ ] Evaluations framework (Xcode 27 — the Swift counterpart to the Python eval pipeline)

## Chapter summary (Summary tab)

- **0:00 Introduction** — recap of the Foundation Models Framework (guided generation, tool calling) and the macOS 27 additions: image inputs and server model access.
- **1:22 Introducing the fm CLI and Python SDK** — two new non-Swift entry points to Apple Foundation Models on macOS: the pre-installed `fm` terminal tool and the Python SDK for ML engineers.
- **3:23 Command line tool** — browsing `fm` commands; `fm chat` interactive conversation; `/model` to switch on-device ↔ PCC; `/save` to resume sessions.
- **5:02 fm respond and structured output** — inline scripting; `--model` and `--image` options; combining `fm schema object` with `--schema` for structured JSON.
- **6:11 Automating file management with fm** — shell-script demo sorting a messy presentation folder; classify draft vs. final, emit structured JSON, route files to backup and archive.
- **8:52 Python SDK** — install requirements (Python 3.10+, Xcode, Apple Silicon); core features mirror Swift (text/image inputs, streaming, tool calling, guided generation); value for ML engineers and rapid prototyping.
- **9:42 Prompting, tool calling and guided generation** — grocery-app prototype; `LanguageModelSession`, `session.respond`, a `get_past_orders` tool, and `fm.generable` for typed `ItemsSuggestion` output.
- **10:44 Building an evaluation pipeline in Python** — Jupyter + Pandas + matplotlib; evaluate three cart-completion prompts; on-device model generates, server judge scores (excess/missing/hallucinated items), charts guide iteration.
- **15:20 Next steps** — explore `fm` in Terminal; visit the Python SDK GitHub for example snippets; build an evaluation pipeline to measure and improve prompt quality.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
