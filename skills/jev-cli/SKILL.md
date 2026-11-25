---
name: jev-cli
description: Use this skill's bundled Jev CLI for one or more Noul, Choice, and Score questions without writing application code or hand-building HTTP. Supports one-request multi-question batches from flags or an official System One JSON request. Use the TypeSafe SDK/API for image assets, Box, or maintained application integrations.
---

# Use Jev from the command line

Use the bundled CLI for a direct Jev request. It saves request-building and response-parsing work. It does not reduce Jev's network latency. It is a convenience wrapper around the official System One API.

## Locate the bundled script

This skill includes `scripts/jev-cli`. Skill installers copy the complete skill directory, including this script. No global CLI install or `pip install` is needed.

Resolve this skill's base directory from the location supplied by your runtime or from the loaded `SKILL.md` path. Do not assume the current working directory or that `jev-cli` is on `PATH`.

```sh
SKILL_DIR="<absolute path to this skill directory>"
JEV_CLI="$SKILL_DIR/scripts/jev-cli"
```

Replace the placeholder with this skill's installed directory. Run the script with `python3 "$JEV_CLI" ...`. It uses only the Python standard library. Check that Python 3 exists.

Check that `TYPESAFE_API_KEY` is set. Check presence only. Never print, request, or copy its value into a command. The script reads it from the environment.

For a human, running the script with no arguments opens the interactive menu in a terminal. The wizard gathers one or more Noul, Choice, and Score questions; after each question, Enter sends the current set as one request, while `y` adds another question. After the answers, Enter starts another set for the same state and `t` changes the state. It does not ask the user to write JSON. For an agent or script, always use a subcommand. A no-argument call can prompt and block automation.

## Choose the right interface

| Task | Use |
| --- | --- |
| One question about one text state | `noul`, `choice`, or `score` subcommand |
| Several Noul, Choice, or Score questions about one state | `ask` with repeated question flags, or `ask --request FILE`; both send one API request |
| Same question over independent one-line text states | `--lines`; this sends one API request per non-empty line, sequentially |
| Structured state, structured instructions/criteria, or optional Noul criteria | `ask --request FILE` |
| Images, Box, or image assets | Official TypeSafe SDK/API; this CLI does not implement asset upload |
| Maintained application integration, typed SDK responses, or application logic | Official TypeSafe SDK |

TypeSafe's official System One API accepts one JSON request object with one `state` and a map of named `questions`. It answers all those questions in one response. Prefer this batch when questions share a state. Do not send one CLI request per question in that case.

## Send several questions with flags

Use `ask` to define multiple questions in one command. Question IDs become keys in the response's `answers` object.

```sh
python3 "$JEV_CLI" ask -s "$STATE" \
  --noul billing="Is this about billing?" \
  --choice tone="What is the tone?" \
  --choice-criterion tone=calm="Neutral or polite" \
  --choice-criterion tone=angry="Upset or hostile" \
  --score urgency="How urgent is this?" \
  --score-criterion urgency="Can wait" \
  --score-criterion urgency="Needs attention today" \
  -o json
```

- `--noul ID=INSTRUCTIONS` adds a Noul question.
- `--choice ID=INSTRUCTIONS` adds a Choice question.
- `--choice-criterion ID=NAME[=DESCRIPTION]` adds a Choice criterion for that question ID. Repeat it at least twice per Choice.
- `--score ID=INSTRUCTIONS` adds a Score question.
- `--score-criterion ID=LEVEL` adds an ordered Score level for that question ID. Repeat it at least twice per Score, from lowest to highest.
- `-s/--state` supplies the shared state. If omitted, piped stdin supplies it.
- `-m/--model` overrides the model. The default is `TYPESAFE_DEFAULT_MODEL` or `jev-latest`.
- `-o json` returns the raw System One JSON response. This is the default for `ask`.

Use distinct, meaningful question IDs and Choice criterion names. IDs identify answers; Jev judges the question's `instructions` and `criteria`.

## Send an official JSON request

JSON is the official System One request/response format. Write one object, not JSONL, when you want to describe one request containing several questions:

```json
{
  "model": "jev-latest",
  "state": "I was charged twice for my subscription. Please refund it.",
  "questions": {
    "billing": {
      "type": "noul",
      "instructions": "Is this about billing?"
    },
    "tone": {
      "type": "choice",
      "instructions": "What is the tone?",
      "criteria": {
        "calm": "Neutral or polite",
        "angry": "Upset or hostile"
      }
    },
    "urgency": {
      "type": "score",
      "instructions": "How urgent is this?",
      "criteria": ["Can wait", "Needs attention this week", "Needs attention today"]
    }
  }
}
```

Send that single request with:

```sh
python3 "$JEV_CLI" ask --request request.json -o json
# Or read the JSON request from stdin:
cat request.json | python3 "$JEV_CLI" ask --request - -o json
```

`--request` accepts the System One request shape. The CLI fills in `model` from `-m` or its default if the field is absent. It supports Noul, Choice, and Score. It rejects `assets` because image uploads need a different API transport. Use the SDK/API for Box or assets.

Use flag mode for compact commands. Use JSON request mode when flags become awkward or when you need structured state, instructions, criteria, or optional Noul criteria. Both modes make one API request and return the same raw response.

## Ask one question

For one question, use the shorter subcommands:

```sh
python3 "$JEV_CLI" noul "Is this a refund request?" -s "$STATE" -o json

python3 "$JEV_CLI" choice "What is the tone?" \
  -c calm="Neutral or polite" -c angry="Upset or hostile" \
  -s "$STATE" -o json

python3 "$JEV_CLI" score "How urgent is this?" \
  -c "Can wait" -c "This week" -c today \
  -s "$STATE" -o json
```

These commands also accept `-f FILE`; `-f -` reads stdin. If stdin is piped and neither `-s` nor `-f` is given, it is the state.

Avoid putting private or large state text in command arguments. Arguments can appear in shell history, process listings, and agent logs. Prefer stdin or `-f` when practical. The human interactive menu prints a shell-equivalent command containing the state; do not copy or log it if the state is sensitive.

## JSON and JSONL

- **JSON** is one complete object. This is the official System One request and response format. Use `ask --request` or `ask` with question flags to send one request and receive all its answers together. `-o json` prints the raw API response, including `model`, `answers`, and `usage`.
- **JSONL** is a stream of independent JSON objects, one per line. It is not the System One request format. In this CLI, `--lines -o jsonl` processes each non-empty input line as a separate state and writes one normalized result record per state. It makes multiple API calls sequentially; it does not batch questions.

For `--lines`, `index` is the original one-based input line number. Blank lines are skipped, so indexes can have gaps. A success record uses the CLI-normalized fields `index`, `ok`, `kind`, `value`, `label`, `confidence`, and `probabilities`; a failed item uses `index`, `ok: false`, and `error`. For example:

```json
{"index":1,"ok":true,"kind":"noul","value":0.91,"label":null,"confidence":null,"probabilities":null}
```

Exit `0` means all items succeeded, `2` means all failed, and `3` means some failed.

Other exit codes: `1` means usage, input, or configuration error (including a missing API key); `2` means API or network failure; `130` means Ctrl-C. Keep stdout for data and stderr for diagnostics. Check both the exit code and each JSONL record's `ok` field. Never treat a failed item as a negative Noul or a Choice/Score answer.

## Interpret Jev's answers

- Noul's `noul` is the probability that the statement is true. It is not a degree score.
- Choice returns the selected `choice` and probabilities for every supplied criterion.
- Score is a numeric position and can fall between its ordered levels. Inspect its level `probabilities`; do not replace the distribution with a rounded level.
- Choice and Score `confidence` measures how concentrated the probability distribution is. It is not, in general, the probability that the answer is correct.

## Official references

- [System One API](https://docs.typesafe.ai/api/system-one.md)
- [Question types](https://docs.typesafe.ai/primitives.md)
- [Noul](https://docs.typesafe.ai/primitives/noul.md)
- [Choice](https://docs.typesafe.ai/primitives/choice.md)
- [Score](https://docs.typesafe.ai/primitives/score.md)
- [Confidence](https://docs.typesafe.ai/confidence.md)
- [TypeSafe client SDKs](https://docs.typesafe.ai/sdk.md)
