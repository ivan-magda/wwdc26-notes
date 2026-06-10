---
title: "Build AI-powered scripts with the fm CLI and Python SDK — Full Digest"
session: WWDC26 · 334
url: https://developer.apple.com/videos/play/wwdc2026/334/
duration: 17m
speakers: Eric Gourlaouen
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 334 — Build AI-powered scripts with the fm CLI and Python SDK

## TL;DR

Apple Foundation Models used to be reachable only from Swift. macOS 27 adds two
non-Swift front doors:

1. **The `fm` command line tool** — pre-installed with macOS 27, available straight
   from Terminal. `fm respond` for one-shot/scriptable prompts, `fm chat` for an
   interactive REPL, `fm schema` to build a JSON generation schema. Defaults to the
   on-device model; `--model pcc` reaches Private Cloud Compute. Because structured
   output is just JSON on stdout, it drops straight into shell pipelines (`jq`).
2. **The Foundation Models SDK for Python** (`pip install apple_fm_sdk`) — mirrors the
   Swift API surface (sessions, tool calling, guided generation, text + image inputs,
   streaming) so ML engineers and data scientists can prototype prompts in Python and
   plug into the open-source ML/data ecosystem.

The throughline demo: a file-triage shell script that uses the model to sort messy
draft vs. final filenames, and a Jupyter/Pandas/matplotlib **evaluation pipeline**
that scores three prompt variants with a server judge model — the Python answer to
Xcode 27's Swift Evaluations framework.

---

## 1. Why these tools exist

Recap: the Foundation Models Framework shipped at WWDC25 in Swift, with guided
generation (structured outputs) and tool calling (model reaches app context). macOS 27
/ iOS 27 add image inputs in prompts and server-model access through the same Swift
API. The pitch for these models: no API key, no cloud API cost, easy setup, scaling
from text extraction up to agentic workflows.

The gap this session fills: until now those models were **Swift-only**. The `fm` CLI
and Python SDK open them to terminal automation and the Python ecosystem.

## 2. The `fm` command line tool

Pre-installed with macOS 27. Type `fm` to list commands — `respond`, `chat`, `schema`,
and more.

### `fm chat` — interactive REPL

Starts a conversation with the on-device model in the terminal. In-session commands:

- `/model` — switch the conversation to the Private Cloud Compute model.
- `/save` — save the current conversation to resume later.

Good for getting a "first pulse" of the model and exploring whether a prompt idea
performs, without building anything.

### `fm respond` — inline / scriptable

Run with a prompt, get the model's response as stdout. Options shown:

```bash
$ fm respond "Provide a basic regex in Swift to parse an email address"

$ fm respond "Provide a comprehensive regex in Swift to parse an email address" --model pcc

$ fm respond "What app is the user using in this screenshot?" --model pcc \
	--image Screenshot.png
# The user is using the Mail app.

$ fm respond --help
```

- `--model pcc` switches from the default on-device model to Private Cloud Compute
  (bigger model, better on complex problems, but **usage limits** apply). On-device is
  the default and "always available."
- `--image` attaches an image to the prompt.

### Structured output: `fm schema` + `--schema`

```bash
$ fm schema object --name AppsIdentified --string app_names --array > schema.json
$ fm respond "What apps are the user actively using in this screenshot?" \
	--image Screenshot.png --model pcc --schema schema.json
# {"app_names": ["Messages", "Mail", "Calendar"]}
```

`fm schema object` builds a JSON generation schema from typed field flags
(`--string app_names --array` → a string array). Feed it back into `fm respond` via
`--schema` and the model is constrained to emit matching JSON.

## 3. Automation demo — triaging a messy asset folder

The problem: a finished presentation project left a folder full of drafts and finals
with messy, unpredictable names. Goal: keep only finals (back them up), move drafts to
an archive disk, and make it rerunnable as a script. Using `fm` lets the script lean
on a language model to classify draft vs. final even when names don't sort cleanly.

```bash
fm schema object --name "TriagedFileList" \
    --string 'final_files' --array \
    --string 'draft_files' --array > /tmp/schema.json

output=$(fm respond \
    --instructions "I just completed a project, and I need help triaging the latest version of the files from the previous versions. I will give you a list of files. Return a list of the latest files [...], and then return separately a list of all draft files [...]." \
    "This is the list of all files:\n\n${files_list}" \
    --schema /tmp/schema.json
)

echo "${output}" | jq -r '.final_files[]' | while read -r file; do
    cp "${DIRECTORY_TO_TRIAGE}/${file}" "${FINAL_FILES_STORAGE_DIRECTORY}"
done

echo "${output}" | jq -r '.draft_files[]' | while read -r file; do
    mv "${DIRECTORY_TO_TRIAGE}/${file}" "${DRAFT_FILES_STORAGE_DIRECTORY}"
done
```

Flow: load the file list → `fm respond` with `--instructions` + the list + `--schema`
→ parse the JSON (`final_files`, `draft_files`) with `jq` → `cp` finals to backup,
`mv` drafts to archive. The schema-constrained JSON is what makes the output safe to
pipe into shell logic.

## 4. The Foundation Models SDK for Python

```bash
pip install apple_fm_sdk
```

Requirements: **Python 3.10+**, **Xcode installed**, **Apple Silicon Mac**. Installable
via pip or any package manager. Gives Python code access to the same on-device model,
with the core framework features (text + image inputs, streaming, tool calling, guided
generation). The API is deliberately close to Swift, so Foundation Models Swift
developers will recognize it.

### Prompting — sessions

```python
import apple_fm_sdk as fm

INSTRUCTIONS = "You're an AI assistant for Cupertino Mart, a grocery store with in-app ordering."

async def answer_question(prompt: str) -> str:
	session = fm.LanguageModelSession(instructions=INSTRUCTIONS)
	return await session.respond(prompt)
```

Create a `fm.LanguageModelSession` (optionally with instructions), then `await
session.respond(prompt)`. The running example is a grocery-ordering app, prototyped in
Python before being implemented in Swift.

### Tool calling

```python
class GetPastOrdersTool(fm.Tool):
  name = "get_past_orders"
  description = "Retrieves information about this user's past orders."

  @fm.generable("Past orders query parameter")
  class Arguments:
  	number_orders: str = fm.guide("How many of the last orders to retrieve")

  @property
  def arguments_schema(self) -> fm.GenerationSchema:
  	return self.Arguments.generation_schema()

  async def call(self, args: fm.GeneratedContent) -> str:
  	number_orders = args.value(int, for_property="number_orders")
  	return await Orders.load_last_orders(user_id=user_id, amount=number_orders)
```

A tool is an `fm.Tool` subclass with `name`/`description`, an `@fm.generable`
`Arguments` class whose fields use `fm.guide(...)`, an `arguments_schema` property, and
an async `call` that reads typed values via `args.value(int, for_property=...)`. Lets
the model fetch order history for personalized answers.

### Guided generation

```python
@fm.generable("Suggested items")
class ItemsSuggestion:
	item_names: list[str] = fm.guide("Names of the suggested items")

async def generate_suggested_cart_items(user_input: Optional[str]) -> ItemsSuggestion:
	session = fm.LanguageModelSession(instructions=INSTRUCTIONS, tools=load_tools())
	prompt = """Using the tools to load the user's previous orders, \
              return a list of items the user has already ordered [...]"""
	if user_input is not None:
		prompt += f"\nAccount for the following request from the user: {user_input}"
	return await session.respond(prompt, generating=ItemsSuggestion)
```

Decorate the output class with `@fm.generable`, annotate fields with `fm.guide`, and
pass the class as the `generating=` argument to `respond` to get a typed object back.

## 5. Case study — an evaluation pipeline in Python

The real payoff of being in Python: tap the open-source ML/data ecosystem. Feature
under test: predict what a user would add to their cart from previous orders, while
accounting for items already in the cart. Three implementations differ only by prompt:

1. **minimal** prompt
2. **descriptive** prompt (task described in more detail)
3. **comprehensive** prompt (a full list of rules)

Pipeline (run from a Jupyter Notebook):

1. Use a **large server model** to generate evaluation data — inputs plus expected
   outputs.
2. For each input, generate outputs with each of the three on-device-model
   implementations; store inputs + outputs as rows in a **Pandas DataFrame**.
3. Run **judge functions backed by a server model** to score each output on chosen
   criteria; store the metrics back in the DataFrame.
4. Plot with **matplotlib** to compare prompt variants visually.

Findings from the charts (concrete, useful as a teaching example):

- The **detailed/descriptive** prompt produced a high rate of **generation errors** —
  e.g. hitting the model's max context window size.
- The two **less detailed** prompts tended to add **excess items**; the more detailed
  one had fewer excess items.
- But the more detailed prompt **missed more expected items**.
- The first (minimal) prompt led to more **hallucinated items** added to the cart.

The point: quantify accuracy, iterate fast in the notebook without rebuilding the
project. This is the Python counterpart to the Swift **Evaluations framework** (Xcode
27) mentioned for Swift developers.

## 6. Next steps (from the talk)

- Explore the `fm` command line tool from Terminal; try the options and features.
- Visit the Python SDK **GitHub repository** for example snippets and documentation on
  advanced workflows.
- Build your own evaluation pipeline: pick a model use case, find working prompts, then
  quantify them against an evaluation dataset.
