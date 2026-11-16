# Question schema

Each true/false **pair** is one YAML file: `questions/<area>/<id>.yaml`. The folder names match the article domains in [ai-knowledge-graph](https://github.com/john1hsu/ai-knowledge-graph) (transformers-and-llms, reinforcement-learning, governance-and-policy, …). The quiz serves one side of a pair at random and never both sides in one session.

```yaml
id: ai-000002                     # stable, never reused
area: transformers-and-llms       # = source article's domain = folder
kind: mechanism                   # definition | origin | mechanism | evidence | application | figure
topic: Attention Is All You Need  # the source article's title, shown after answering
topic_type: Work                  # the article's class: Architecture, Algorithm, Technique, Model, Benchmark, Policy, Work, ...
maturity: foundational            # copied from the article: historical | foundational | established | emerging | frontier
true_statement: The original Transformer dropped recurrence and convolution entirely, relying on attention and feed-forward layers.
false_statement: The original Transformer kept a recurrent layer in the encoder, adding attention and feed-forward layers on top.
explanation: The paper's point was that attention alone was enough. Removing recurrence let every position be processed in parallel, which is why it trained much faster than RNN translation models.
as_of: '2017'                     # only on figure pairs: the year or period the number is for
source:
  repo: john1hsu/ai-knowledge-graph
  path: articles/transformers-and-llms/attention-is-all-you-need.md
  commit: <source repo HEAD when written or last verified>
  blob: <git blob of the article at that point>
concepts: [kt:attention-is-all-you-need, kt:recurrent-neural-network]   # graph node ids; the source article's id comes first
related: []                       # optional: other pair ids
supersedes: ai-000041             # only on a pair that replaced an outdated one
difficulty: 2                     # 1 foundations | 2 practitioner | 3 research-level
created: '2026-11-16'
status: active                    # active | stale | retired
stale_reason: ...                 # only while stale
retired_reason: ...               # optional, on retired pairs
```

`schema/question.schema.json` has the same rules as a JSON Schema for editors.

## Kinds

Each pair says what it teaches, so a topic gets learned from several sides and you can drill one kind (for example "How it works" to understand an architecture, or "Who and when" to place a landmark paper).

| kind | App label | Tests |
| --- | --- | --- |
| definition | What it is | What the idea is, and what it is not |
| origin | Who and when | Which paper, lab or person introduced it, and when |
| mechanism | How it works | Architecture, algorithm, objective, training or inference steps; cause and effect |
| evidence | Results and limits | What experiments and benchmarks show; failure modes, limits and critiques |
| application | In practice | How it is used: deployments, tooling, evaluation practice, regulation in force |
| figure | Numbers and dates | A dated number such as parameters, tokens, a benchmark score or compute (`as_of` required, and dated in the statement) |

## Lifecycle

A pair changes only when its source article does, but AI articles change often (new results, new model versions, regulation milestones). Pairs carry the article's `maturity`, and when stale, dated figures and pairs on `emerging` or `frontier` topics are re-checked first.

| Status | Meaning | In the quiz? |
| --- | --- | --- |
| active | Matches the latest version of its source article | Yes |
| stale | The source article changed (or disappeared) since the pair was verified | Only with `--include-stale` |
| retired | The fact changed (a newer pair `supersedes` it), the article is gone, or the pair repeated a fact another pair already tests | No |

## Checks (`scripts/validate.py`)

| Check | Fails when |
| --- | --- |
| Schema | A required field is missing, malformed or unknown |
| Unique ID | An ID repeats, or the file name or folder doesn't match |
| Kind | `kind` isn't one of the six kinds |
| Dated figures | A `figure` pair has no `as_of`, `as_of` names no year, or the true statement doesn't mention it |
| Dated records | A statement says "state of the art", "SOTA", "most capable", "the largest model", "latest", "currently" or similar without naming a year |
| Maturity | `maturity` isn't one of the article maturities |
| Duplicate | A statement closely matches one in another non-retired pair |
| Same fact | Two non-retired pairs, from any articles, have true statements that state one fact in different words: most of their words match, or they share their numbers and most of the shorter statement's words |
| Pair quality | The two statements are identical or differ only by negation |
| Explanation | Shorter than 60 characters |
| Statement form | A statement is a question, under 20 or over 280 characters |
| Source | Repo, path, commit or blob is malformed; with `--source`, the article's domain differs, or an active pair's article is missing or changed (run `refresh.py mark-stale` first; stale pairs are exempt because they are waiting to be refreshed or retired) |
| Supersedes | Points to an ID that is missing or not retired |
| Exam-like wording | "Which of the following", "Choose two", "according to the article" and similar |
