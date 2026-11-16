# AI quiz bank

Learn artificial intelligence one swipe at a time. A bank of paired true/false questions on ML foundations, deep learning, transformers and LLMs, training and fine-tuning, inference and optimization, retrieval, prompting and reasoning, agents and tool use, reinforcement learning, vision and multimodal models, evaluation, safety and security, governance and policy, infrastructure and AI history, generated daily from the cited articles in [ai-knowledge-graph](https://github.com/john1hsu/ai-knowledge-graph). Each pair holds a true statement and a plausible false twin; quizzes show one side at random.

## Practice on your phone

**Open the app: [john1hsu.github.io/ai-quiz-bank](https://john1hsu.github.io/ai-quiz-bank/)**

The app in `app/` is published with GitHub Pages. It works like Instagram Reels: an endless, full-screen feed with one question per screen.

| Gesture | What it does |
| --- | --- |
| Swipe right | Answer **true** |
| Swipe left | Answer **false** |
| Swipe up | Next question (only after you've answered) |
| Double-tap | Save the question (or tap the bookmark on the right) |

There are no answer or next buttons. New users get quiet hints that fade away once they've learned the gestures: a gentle sway if they hesitate and a small up-chevron after answering. Screen readers still get hidden answer and next buttons.

Built for learning, not just scoring:

- **Learn why.** After each answer the question is replaced by a green check or a red ✗ and an explanation of why it's true or false.
- **Read the topic.** Tap the book icon on the right to open the full cited article on the [ai-knowledge-graph website](https://john1hsu.github.io/ai-knowledge-graph/).
- **Missed.** Questions you get wrong go into a Missed feed until you answer them right, so you can review your gaps.
- **Learn a topic from every side.** Each question has a type: *What it is*, *Who and when*, *How it works*, *Results and limits*, *In practice*, or *Numbers and dates*. Tap the filter chip to drill one type or one AI area, switch to Missed or Saved, and see today's score and your best streak.

There's no skipping and no going back: you answer each question before the next appears, and you can't scroll back to earlier ones. Questions are drawn completely at random with no weighting; each one shows once before any repeats. Scores, missed and saved questions stay on your phone; "Copy scores" exports them. On a computer, use ← / → to answer, ↓ or Space for the next question, and S to save. Add it to your home screen to use it offline.

## Practice in the terminal

```bash
pip install -r requirements.txt
python3 scripts/quiz.py                                   # 10 random questions
python3 scripts/quiz.py --area transformers-and-llms --n 25
python3 scripts/quiz.py --kind mechanism                  # how architectures and algorithms work
python3 scripts/quiz.py --concept kt:self-attention
python3 scripts/quiz.py --list                            # areas and question types
```

At the end it lists the topics you missed. Scores go to a git-ignored `.quiz-progress.jsonl`, or `--progress <path>`.

## How it works

| Piece | What it does |
| --- | --- |
| `questions/<area>/ai-000123.yaml` | One pair per file; areas match the source repo's domains. Format in `SCHEMA.md` |
| `index/questions.json` | Generated list the app and `quiz.py` read |
| `coverage/coverage.json` | Generated pair counts per article, area and question type |
| `scripts/validate.py` | All quality checks; runs before every commit and in CI |
| `scripts/generate.py` | Picks the next article and turns drafted pairs into files |
| `scripts/refresh.py` | Marks pairs stale when their source article changes, and verifies, supersedes or retires them |
| `scripts/build_index.py` | Writes the index and coverage files |
| `CLAUDE.md` | The daily run's playbook |

### Daily run

Once a day, a scheduled run handles up to 100 pairs and pushes them to `main` as one combined commit, only if every check passes:

1. Mark stale any pair whose source article changed or was removed.
2. Re-check every stale pair against the current article (dated figures first, then fast-moving emerging and frontier topics).
3. Spend the rest of the 100-pair budget on new pairs, up to 3 per article per run, starting with the articles that have the fewest pairs (up to 6 per article) and spreading them across AI areas. Each article is covered from different sides: what it is, which paper or lab introduced it, how it works, what the results show and where it breaks, and how it's used.

### When an article changes

ai-knowledge-graph adds an article every hour and updates old ones as the field moves (new results, new model versions, regulation milestones). When an article changes, its pairs are marked stale and re-checked:

| Outcome | Action | ID |
| --- | --- | --- |
| Still true | Re-stamped with the article's latest version, back to active | Same |
| Fact changed | Old pair retired; new pair with `supersedes` | New |
| Article gone | Pair retired | Same |

Stale pairs are left out of quizzes.

## Local setup

```bash
pip install -r requirements.txt
git clone --depth 1 https://github.com/john1hsu/ai-knowledge-graph .source/ai-knowledge-graph
python3 scripts/validate.py --source .source/ai-knowledge-graph
python3 -m http.server   # then open http://localhost:8000/app/
```

Questions that use a record such as "state of the art" or "the largest model" always say which year it was true in, because AI records go out of date fast. This is a study resource; every fact comes from a cited article, not from memory.
