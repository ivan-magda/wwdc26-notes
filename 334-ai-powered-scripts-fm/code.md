# Code samples — Session 334

Extracted from the Code tab. Labels derived from each snippet's content.

## fm respond — basics, PCC model, image input, schema-constrained output

```bash
$ fm respond "Provide a basic regex in Swift to parse an email address"
# Here is a basic regex to parse an email address in Swift: [...]

$ fm respond "Provide a comprehensive regex in Swift to parse an email address" --model pcc
# [...] Here's a robust Swift implementation using 'NSRegularExpression' to validate a typical email address:

$ fm respond "What app is the user using in this screenshot?" --model pcc \
	--image Screenshot.png
# The user is using the Mail app.

$ fm schema object --name AppsIdentified --string app_names --array > schema.json
$ fm respond "What apps are the user actively using in this screenshot?" \
	--image Screenshot.png --model pcc --schema schema.json
# {"app_names": ["Messages", "Mail", "Calendar"]}

$ fm respond --help
```

## File-triage automation script (schema + jq routing)

```bash
fm schema object --name "TriagedFileList" \
    --string 'final_files' --array \
    --string 'draft_files' --array > /tmp/schema.json

output=$(fm respond \
    --instructions "I just completed a project, and I need help triaging the latest version of the files from the previous versions. I will give you a list of files. Return a list of the latest files (i.e., all files that, you can infer from their name in the list, are the latest versions), and then return separately a list of all draft files (i.e., all files that weren't considered final)." \
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

## Install the Python SDK

```bash
pip install apple_fm_sdk
```

## Python — session + respond

```python
import apple_fm_sdk as fm

INSTRUCTIONS = "You're an AI assistant for Cupertino Mart, a grocery store with in-app ordering."

async def answer_question(prompt: str) -> str:
	session = fm.LanguageModelSession(instructions=INSTRUCTIONS)
	return await session.respond(prompt)
```

## Python — defining a tool (fm.Tool + fm.generable arguments)

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

## Python — guided generation into a typed object

```python
@fm.generable("Suggested items")
class ItemsSuggestion:
	item_names: list[str] = fm.guide("Names of the suggested items")

INSTRUCTIONS = "You're an AI assistant tasked with returning potential grocery items that the user might be interested in."

async def generate_suggested_cart_items(user_input: Optional[str]) -> ItemsSuggestion:
	session = fm.LanguageModelSession(instructions=INSTRUCTIONS, tools=load_tools())
	prompt = """Using the tools to load the user's previous orders, \
              return a list of items the user has already ordered \
              and that they might be interested in again \
              as they're getting ready to place a new grocery order."""
	if user_input is not None:
		prompt += f"\nAccount for the following request from the user: {user_input}"
	return await session.respond(prompt, generating=ItemsSuggestion)
```

---

## API facts surfaced by the code

- CLI verbs seen: `fm respond`, `fm chat`, `fm schema object`; flags `--model pcc`, `--image`, `--instructions`, `--schema`, `--help`.
- `fm schema object` builds a JSON schema with typed fields: `--string <name>` plus `--array` makes a string array. Piped to a file, then fed back via `--schema`.
- Structured output is plain JSON on stdout, so it composes with `jq` in shell.
- Python import name: `apple_fm_sdk` (aliased `fm`).
- Python session API: `fm.LanguageModelSession(instructions=..., tools=...)`, `await session.respond(prompt, generating=...)`.
- Tools: subclass `fm.Tool` with `name`, `description`, an `@fm.generable` `Arguments` class (fields use `fm.guide(...)`), an `arguments_schema` property, and an async `call(self, args)` reading values via `args.value(int, for_property=...)`.
- Guided generation: decorate an output class with `@fm.generable("...")`, annotate fields with `fm.guide("...")`, pass the class as `generating=` to `respond`.
