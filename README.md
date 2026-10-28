# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter sorts posts from r/culinary, a public subreddit where home cooks and culinary students share food, by what the poster wants from the community. `showcase` posts present a finished dish without asking for anything,
`rate_request` posts ask for a rating or critique of a dish, and `question` posts ask for information or advice. The classifier reads each post's title and body text, since most posts on the subreddit are photos. A tool like this
could send critique requests to people who like giving feedback and questions to people who like answering them.


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `showcase`

**Definition:** The poster presents a dish they made, with or without a description of how they made it, and does not ask anyone to judge it or ask the community a question.

**Example 1:**
> Kunafa Prawns. Garlic and paprika marinated Kunafa wraped deep fried prawns served on top of creamy lebneh finished with chilli oil and spiced nuts.

**Example 2:**
> Look what I made today with just 1 chicken wing ,no oil I turned it into a feast

### `rate_request`

**Definition:** The poster shows a finished dish they made and asks for feedback on it (a rating, a critique, or advice on how to improve or plate it), whether as a request ("rate my", "smash or pass") or as a question ("how does this look?", "what would you change?").

**Example 1:**
> How does my chicken look

**Example 2:**
> Tilapia on top of white rice with creamy lime sauce, and broccolini. Tell me what I can improve. Still trying to learn how to plate. Looking back, adding the sauce on top of the tilapia wasn’t the smartest idea. The overall dish did taste great.

### `question`

**Definition:** The poster asks a general question or asks for information (about techniques, ingredients, equipment, food safety, careers, recipes, or a dish still in progress) rather than for feedback on a finished dish they're showing.

**Example 1:**
> How do I make vanilla cakes that stay moist even after 2–3 days in the fridge?

**Example 2:**
> What is this at the top of my chicken stock and what do I do with it? I only had carcasses and neck bones at hand so I used those, did a basic mirepoix mix and skimmed every 20 mins for about an hour. (I used a Joshua weissman recipe). The yellow bit at the top of both has like a skin on top and it’s greasy.

### The hardest boundary

**Which two labels:** `showcase` and `rate_request`

**The decision rule I used every time:** If the post explicitly asks for a rating, judgment critique, or ways to improve the dish, it's `rate_request`. Otherwise it's `showcase`, even if the poster criticizes their own dish ("the meat buns were lowk raw too") or describes it in detail.

**A second rule, for `rate_request` vs `question`:** Look at what the post wants feedback on, not whether it's phrased as a question. If it asks how a finished dish they're showing looks, or how to improve or plate it, it's `rate_request`, even as a question ("does this cured salmon look right?"). If the dish is still in progress (a marinade, a stock, dough that won't rise), or they want other people's recipes or ideas, or it's about cooking in general, it's `question`, even if a photo of their food is attached.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** r/culinary, a public subreddit. Most posts there
are photos, so each row is the post's title plus any body text, copied in whole
and unedited.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
- **20 rows (`cold`):** I labelled these myself from my definitions, with no AI help.
- **21 rows (blank note):** I also labelled these myself, without AI.
- **109 rows (`pre-labeled`):** Claude (an AI assistant) pre-labelled these from my written definitions and flagged the ones it was unsure about. I read every one and corrected the labels I disagreed with, starting with the flagged rows.

Partway through, I tightened the line between `question` and `rate_request`. Feedback on a finished dish is `rate_request`. A general question, or a question about a dish still in progress, is `question`. I then re-checked the existing labels against the new wording.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `showcase` | 85 | 57% |
| `question` | 47 | 31% |
| `rate_request` | 18 | 12% |
| **Total** | **150** | 100% |

**Why 150 and not 200:** I ran out of time to collect the last 50 posts, so I
stopped at the brief's floor of 150. The cost falls mostly on `rate_request`.
With only 18 examples, it will have about 3 posts in the test split, so its F1
will swing a lot between seeds. It also misses my own balance target (criterion
4: every label at least 20%). The next 50 posts would have gone almost entirely
to `rate_request`.

One more thing about the data: about 25 of the `showcase` posts seem to come
from a single prolific poster with a very consistent style ("Wok-Tossed…",
"I had some ___ in the fridge…"). The model may partly learn that writing style
instead of the label.

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "So, this is my staple food, from my country, fresh (matooke)or green bananas. With meat stew and groundnut sauce. This is very healthy since it's all fresh garm produce. (The garnishing was hard but I tried)"
>
> *Could have been:* `showcase` or `rate_request`
>
> *I chose, because:* `showcase`. "The garnishing was hard but I tried" sounds like the poster wants feedback, but they never ask for it. My decision rule says self-criticism without an explicit request for a rating or critique stays `showcase`.

**2.**
> *The post:* "French omelette and berry compote. I’m new to cooking and this was my first attempt at making an omelette and second attempt making a berry compote sauce. I think I cut the onions a bit too thick compared to what I’ve seen and my folding was a bit messy but I’m open to suggestions/tips"
>
> *Could have been:* `rate_request` or `question`
>
> *I chose, because:* `rate_request`. "Open to suggestions/tips" comes right after they critique their own finished dish, so it's asking how to improve that dish. By my second rule, that makes it `rate_request`, not a general cooking `question`.

**3.**
> *The post:* "I will fight people who disagree. Carbonara is just cheesy mayo pasta with pancetta."
>
> *Could have been:* `question`, or none of my labels
>
> *I chose, because:* `question`. It doesn't present a dish, so it isn't `showcase`, and it doesn't ask for a rating. It's an opinion posted to start a debate, which my taxonomy doesn't really cover. I put it in `question` because it invites the community's opinions, but it shows a gap: opinion posts with no dish and no question. They were rare enough in r/culinary that I didn't add a fourth label for them.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed -->
3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42.

**Device:** `mps` (Apple Silicon GPU), torch 2.14.1. Training took about 20 seconds.

**Anything I changed from the defaults, and why:** Nothing. I used the
notebook's default settings.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot of next unit's variance. -->
Stratified 70/15/15 split of 150 posts: 104 train, 23 validation, 23 test.

| Label | Train | Val | Test |
|---|---|---|---|
| `showcase` | 59 | 13 | 13 |
| `question` | 33 | 7 | 7 |
| `rate_request` | 12 | 3 | 3 |

Two labels have fewer than 8 posts in the test split: `question` (7) and `rate_request` (3). Their F1 scores rest on very few posts and will move a lot between seeds. `rate_request` is the bigger concern: a single post changes its score by roughly a third.


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1: refining the label definitions**

- *What I asked for:* I asked Claude to write one-sentence definitions for my three labels, based on the 41 posts I had collected and labelled myself.
- *What came back:* Definitions where `rate_request` required an explicit ask ("rate my", "critique") and `question` covered anything other than a verdict on the poster's dish. The line between the two depended on how the post was phrased.
- *What I changed:* That line didn't fit my data, because many rating requests are phrased as questions ("How does my chicken look"). I redefined `question` as general questions and requests for information, and `rate_request` as asking for feedback on a finished dish, whether phrased as a request or a question. Claude raised two edge cases, and I decided both: troubleshooting a dish still in progress (a marinade, a stock) is `question`, and showing a dish but asking for other people's recipes is also `question`.

**Moment 2: checking the CSV for broken quotation marks**

- *What I asked for:* Pasted posts can contain quotation marks that break CSV rows, so I asked Claude to check `labels.csv` for any rows that didn't split into exactly three columns.
- *What came back:* It said every row was fine. But I spotted a post with "Bali belly" in quotation marks that it hadn't mentioned by name, and I asked it to catch everything.
- *What I changed:* I had it scan every row for every kind of quote character. Those quotes turned out to be curly quotes, which are safe in a CSV, and a later batch did have a row missing its columns, which it fixed. From then on I had it run the full check after every batch I added, and I kept all post text unedited.

**Pre-labelling disclosure:** Claude pre-labelled 109 of my 150 posts from my written definitions. Each of those rows has `pre-labeled` in the `note` column. Claude flagged 16 of them as unsure. I read every pre-labelled row and
corrected the labels I disagreed with, starting with the flagged ones. The 20 `cold` rows and 21 other rows (blank note) I labelled myself without AI.


<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

Both models were scored on the same 23 held-out posts (`test_split.csv`, seed
42). The baseline is `facebook/bart-large-mnli` zero-shot, prompted with my
definitions from `label_definitions.txt`, run on `mps`. The trained model is
the Unit 5 run in `results.json`.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.348 | 0.565 | +0.217 |
| Macro F1 | 0.373 | 0.241 | -0.132 |
| F1 — `question` | 0.833 | 0.000 | -0.833 |
| F1 — `rate_request` | 0.286 | 0.000 | -0.286 |
| F1 — `showcase` | 0.000 | 0.722 | +0.722 |

**With and without my definitions:** I also ran the baseline on the bare label
names, with no definitions (`baseline_results_no_definitions.json`).

| Measure | Baseline, my definitions | Baseline, bare label names |
|---|---|---|
| Overall accuracy | 0.348 | 0.783 |
| Macro F1 | 0.373 | 0.669 |
| F1 — `question` | 0.833 | 0.833 |
| F1 — `rate_request` | 0.286 | 0.286 |
| F1 — `showcase` | 0.000 | 0.889 |


**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I didn't write a prediction before looking. By the time I ran the baseline, I already saw in Unit 5 that my trained model predicts `showcase` for every
post, and I saw the baseline's numbers before writing this section. Anything I
wrote here now would be hindsight, so I'm not presenting one.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
My fine-tuning added nothing. The trained model's higher accuracy (0.565)
comes entirely from predicting `showcase` every time. `showcase` is 13 of the
23 test posts, so that's exactly the score of always guessing the biggest
label. It never predicts `question` or `rate_request`, which is why its macro
F1 is lower than the baseline's.

The baseline actually separates my labels. It catches `question` posts well
(F1 0.833), because they are phrased as questions. Its weakness is
`showcase`: with my definitions it gets none right.

The bare-names run suggests why: without definitions, `showcase` F1 rises to
0.889 and accuracy to 0.783. My `showcase` definition is mostly about what the
post *doesn't* do, and a zero-shot entailment model seems to handle that phrasing poorly. The strongest baseline, bare label names at 0.783, beats my
trained model outright.


---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.565 | 0.565 | 0.565 | 0.000 |
| Macro F1 | 0.241 | 0.241 | 0.241 | 0.000 |
| `showcase` precision / recall / F1 | 0.565 / 1.000 / 0.722 | 0.565 / 1.000 / 0.722 | 0.565 / 1.000 / 0.722 | 0.000 |
| `question` precision / recall / F1 | 0.000 / 0.000 / 0.000 | 0.000 / 0.000 / 0.000 | 0.000 / 0.000 / 0.000 | 0.000 |
| `rate_request` precision / recall / F1 | 0.000 / 0.000 / 0.000 | 0.000 / 0.000 / 0.000 | 0.000 / 0.000 / 0.000 | 0.000 |
| Accuracy, most-confident third | 1.000 | 1.000 | 1.000 | 0.000 |
| Accuracy, least-confident third | 0.286 | 0.286 | 0.143 | 0.143 |

The notebook calls a 0.000 accuracy spread "stable enough to trust". Here it
isn't a sign of a reliable measure. All three seeds predicted `showcase` for
every post, and the stratified split gives every seed the same label counts in
test (13 / 7 / 3), so each one scores exactly 13/23. The numbers can't vary
because the model isn't using the text.

**The five criteria across three seeds:**

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.75 | 0.565 | 0.565 | 0.565 | MISSED |
| 2. `showcase` F1 and `question` F1 | each ≥ 0.75 | 0.722 / 0.000 | 0.722 / 0.000 | 0.722 / 0.000 | MISSED |
| 3. `rate_request` F1 | ≥ 0.40 | 0.000 | 0.000 | 0.000 | MISSED |
| 4. Every label's share of `labels.csv` | 20%–60% | `rate_request` 12% | `rate_request` 12% | `rate_request` 12% | MISSED |
| 5. Most-confident third minus least-confident third, accuracy | ≥ 0.20 | 0.714 | 0.714 | 0.857 | MET (see note) |

Criterion 4 is about the dataset, not the model, so it has the same value for
every seed: `labels.csv` is 57% `showcase`, 31% `question` and 12%
`rate_request`.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42 (notebook section 7). Seeds 7 and 2024 produced the same matrix.

| true \ predicted | showcase | question | rate_request |
|---|---|---|---|
| **showcase** | 13 | 0 | 0 |
| **question** | 7 | 0 | 0 |
| **rate_request** | 3 | 0 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
All 7 real `question` posts were predicted `showcase`, and 0 went the other
way. The same is true of all 3 `rate_request` posts. The model hasn't learned
either boundary; it has collapsed everything into my majority label. This
isn't a confusion between two specific labels. Every error points in the same
direction, toward `showcase`.


---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.75 | MISSED | 0.565 on all three seeds. It never reaches the target, and it is exactly the always-`showcase` score (13/23). |
| 2 | `showcase` and `question` F1 | each ≥ 0.75 | MISSED | Both have to clear 0.75 on every seed. `showcase` reaches 0.722 and `question` 0.000 on all three, so neither does. |
| 3 | `rate_request` F1 | ≥ 0.40 | MISSED | 0.000 on all three seeds. The model never predicts `rate_request`. |
| 4 | Label balance in `labels.csv` | every label 20%–60% | MISSED | Measured on the file, not the model. `rate_request` is 18 of 150 rows (12%), under the 20% floor. `showcase` (57%) and `question` (31%) are within range. |
| 5 | Confidence gap | ≥ 0.20 | MET, but not meaningful | The gap is 0.714, 0.714 and 0.857, so it clears 0.20 on every seed. But the model predicts `showcase` for everything, so "right" just means "the post really was `showcase`". It's more confident on `showcase`-looking posts, which makes the gap real arithmetic but not evidence that its confidence tracks correctness across my labels. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 1, 2 and 3 (accuracy, `showcase`/`question` F1, `rate_request` F1):
one cause.** The model never learned to use the text. It predicts my majority
label, `showcase`, for every post. Out of the four common causes, this is
mainly **a task the model can't reach from this much data, with these
settings**, plus **too few examples** for `rate_request`. The evidence:

- **The confusion matrix.** All 23 test posts land in the `showcase` column,
  identically on all three seeds. Accuracy is exactly 13/23 = 0.565, the score
  of always guessing the biggest label.
- **The training log.** With 104 training posts and batch size 16, three
  epochs is only about 21 training steps. Validation accuracy sat at 0.565
  after every epoch (seed 42: 0.565, 0.565, 0.565), and training loss only
  fell from about 1.03 to 0.88. The model stopped before it got past the
  majority guess.
- **The baseline.** The zero-shot model, given nothing but text, reaches F1
  0.833 on `question`. The signal for my labels is in the posts. My
  fine-tuned model just didn't train long enough on enough data to pick it up.
- **`rate_request` specifically.** It has 18 examples, only 12 of them in each
  training split. Even a model that trained properly would struggle to learn a
  label from 12 posts, and its 3 test posts make the F1 swing by about a
  third per post.

**Why this is a model and data problem, not a labelling problem.** My
agreement report shows I do make labelling errors. On the staff set I agreed
77% of the time, and all 7 disagreements were pile (a): I applied a written
rule loosely, always in the same direction (letting first-person phrasing push
`analysis` posts into `reaction`). If the same kind of slip had damaged my own
labels, it would show up in the matrix as confusion on one specific pair,
`showcase` vs. `rate_request` or `rate_request` vs. `question`, and some
correct predictions on the others. That's not what the matrix shows. It shows
the model ignoring the text and predicting one label for everything. Messy
labels on one boundary can't cause that. A model that never got enough
training can. My labelling slips are real, and they're the next thing I'd
check once the model actually trains, but they don't explain these misses.

**Criterion 4 (label balance): cause is data collection.** `rate_request` is
18 of 150 rows (12%), under my 20% floor. This is the **too few examples**
cause, and it's a collection problem, not a model one. I ran out of time at
150 posts, and `rate_request` posts are much rarer on r/culinary than
`showcase` posts. It also feeds into criterion 3: the thinnest label in the
data is the one with no correct predictions.

**Criterion 5 (MET).** No diagnosis needed for a pass. But as noted in the
verdict, it passes for the wrong reason. Once the model predicts more than one
label, this gap will be a real test.

**Revisions:** None. All five criteria produced a clear verdict from the
outputs, so none of them was unmeasurable. The misses stay as they are in
`criteria.md`.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 23 / 30 = 77%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

All seven run the same way: staff said `analysis`, and I said something else.

**1. (sp_001)**
> *The post:* "Pulled the splits from the last five editions. The winning time has moved by 40 seconds total in ten years, but the 100th-place time is four minutes faster. The depth changed, the front didn't."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. Nothing here just happened to the writer, and there's no feeling. It's two checkable numbers, and the contrast between them is the whole claim. That matches the `analysis` definition, and `reaction` ("feeling rather than argument") doesn't fit at all.
>
> *Which pile:* (a)

**2. (sp_002)**
> *The post:* "I tracked sleep against session quality for eleven weeks. Under seven hours the next day's intervals were 6-8 seconds per mile slower, every time bar one. Small sample, one runner, but it's consistent enough that I've stopped scheduling hard days after late shifts."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. It's about the writer's own running, which pulled me toward `reaction`. But it covers eleven weeks, not the last few hours, and the conclusion rests on the measured 6–8 seconds. Rule 3 covers this: personal and recent doesn't outrank a load-bearing fact.
>
> *Which pile:* (a)

**3. (sp_003)**
> *The post:* "The club championship scoring changed in 2019 from top-6 to top-10. Since then our B team has finished above the A team twice. That's not depth improving, it's the scoring rewarding turnout."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. A named date, a named rule change, and a counted result are used to reject a rival explanation. That's an argument from checkable facts. "Our B team" is the only personal element, and it isn't a feeling about something that just happened.
>
> *Which pile:* (a)

**4. (sp_004)**
> *The post:* "Everyone blames the hill at 18 miles. I split the field into people who ran the first half under 1:45 and over, and the slowdown after 18 was almost identical for both groups. If the hill were the problem the fast starters would fall apart harder."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. The post sets up a test, reports what it showed, and draws an inference. It reads as a response to a race, but rule 3 says immediacy isn't the deciding feature when a fact carries the argument, and here it does.
>
> *Which pile:* (a)

**5. (sp_007)**
> *The post:* "Ran the same loop in 4C and in 24C within a fortnight, same effort by heart rate. 90 seconds slower in the heat over six miles. People keep saying heat costs you 'a bit' and it's more than that."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right. It's the writer's own runs, but the last sentence is a general claim ("heat costs more than people say"), and the 90 seconds is the reason for it. By rule 3, the load-bearing fact makes it `analysis` even though it's personal.
>
> *Which pile:* (a)

**6. (sp_008)**
> *The post:* "Went back through the club results going back to 2012. Every single year the number of people who enter the spring marathon and don't start it sits between 18 and 23 percent. It's not a bad-luck year, it's the base rate."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* Staff is right. The ending sounds like a confident claim, which is why I said `hot_take`. But rule 1 asks whether the fact carries the argument. The conclusion ("it's the base rate") only holds because of the 18–23% range across every year since 2012. That range is the argument, not decoration.
>
> *Which pile:* (a)

**7. (sp_010)**
> *The post:* "Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was smaller than the noise between three people."
>
> *Staff said / I said:* `analysis` / `reaction`
>
> *My call, and why:* Staff is right, though this is the closest one. It's a same-day personal session with a hedge ("I'm not saying shoes do nothing"), which is why it read as `reaction` to me. But there's no feeling in it, and the conclusion rests on a checkable comparison (within 10 seconds over 5 miles). The sample is tiny, but the definition asks for a checkable fact that does work, not a strong one. Rule 3 still applies.
>
> *Which pile:* (a)

**Pile counts:** (a) 7 · (b) 0 · (c) 0

**What the pattern in my disagreements tells me:**
Every miss is the same mistake. When a post was about the writer's own running
or their own club, I treated it as `reaction`, even when it was built on a
checkable fact. I put only 3 of 30 posts in `analysis` against staff's 10.
Rule 3 already covers this ("recency does not outrank evidence"), so this
isn't a gap in the definitions. I applied a written rule loosely, and in a
consistent direction: I let surface cues (first person, "our", "I ran")
decide the label instead of what the post is doing. That matters for my own
dataset. It's the same kind of slip that my `question` / `rate_request`
boundary invites, where phrasing ("how does this look?") can pull a post away
from what it's actually asking for.

---

## The Improvement

**What I changed:** One training setting: `EPOCHS` went from 3 to 10 (notebook
section 2). Everything else stayed the same: `labels.csv` (no rows changed or
added), `distilbert-base-uncased`, lr 2e-5, batch 16, max length 128, and the
same seeds 42, 7 and 2024. I ran section 6 with `RUN_LABEL = "after"` on
`mps`. Results are in `results_three_seeds_after.json`.

**Which diagnosis pointed at it:** My M4 diagnosis for criteria 1–3. Three
epochs over 104 posts is only about 21 training steps, and validation accuracy
stayed flat at the majority-guess score (0.565) every epoch. The baseline
showed the signal was in the text. The agreement report showed my labelling
slips couldn't explain a total collapse. So the model hadn't trained long
enough. Adding `rate_request` posts would also fit the diagnosis, but I didn't
have time to collect them. Ten epochs is about 70 steps.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.75 | 0.870 | 0.739 | 0.870 | MISSED |
| 2. `showcase` F1 and `question` F1 | each ≥ 0.75 | 0.929 / 0.933 | 0.889 / 0.625 | 0.929 / 0.933 | MISSED |
| 3. `rate_request` F1 | ≥ 0.40 | 0.000 | 0.000 | 0.000 | MISSED |
| 4. Every label's share of `labels.csv` | 20%–60% | `rate_request` 12% | `rate_request` 12% | `rate_request` 12% | MISSED |
| 5. Most-confident third minus least-confident third, accuracy | ≥ 0.20 | 0.143 | 0.857 | 0.286 | MISSED |

**Before and after, side by side:**

| Criterion | Target | Before (42 / 7 / 2024) | After (42 / 7 / 2024) | Verdict before → after |
|---|---|---|---|---|
| 1. Accuracy | ≥ 0.75 | 0.565 / 0.565 / 0.565 | 0.870 / 0.739 / 0.870 | MISSED → MISSED |
| 2. `showcase` F1 | ≥ 0.75 | 0.722 / 0.722 / 0.722 | 0.929 / 0.889 / 0.929 | part of 2 |
| 2. `question` F1 | ≥ 0.75 | 0.000 / 0.000 / 0.000 | 0.933 / 0.625 / 0.933 | MISSED → MISSED |
| 3. `rate_request` F1 | ≥ 0.40 | 0.000 / 0.000 / 0.000 | 0.000 / 0.000 / 0.000 | MISSED → MISSED |
| 4. Balance | 20%–60% | 12% | 12% | MISSED → MISSED |
| 5. Confidence gap | ≥ 0.20 | 0.714 / 0.714 / 0.857 | 0.143 / 0.857 / 0.286 | MET → MISSED |
| Macro F1 (not a criterion) | — | 0.241 / 0.241 / 0.241 | 0.621 / 0.505 / 0.621 | — |

Confusion matrix after, seed 42 (seed 2024 was the same):

| true \ predicted | showcase | question | rate_request |
|---|---|---|---|
| **showcase** | 13 | 0 | 0 |
| **question** | 0 | 7 | 0 |
| **rate_request** | 2 | 1 | 0 |

Seed 7:

| true \ predicted | showcase | question | rate_request |
|---|---|---|---|
| **showcase** | 12 | 1 | 0 |
| **question** | 2 | 5 | 0 |
| **rate_request** | 0 | 3 | 0 |

**Did it help, and how do I know:** Yes, it fixed the collapse, but it didn't
turn any criterion into a MET. The model now uses the text. Accuracy rose
from 0.565 to 0.739–0.870, `question` F1 went from 0.000 to 0.625–0.933, and
macro F1 more than doubled. On two seeds it beats both baselines, including
the bare-names zero-shot run (0.783). But criteria 1 and 2 still miss because
of a single seed: seed 7 (accuracy 0.739, `question` F1 0.625) falls below
both targets, while seeds 42 and 2024 clear them easily.

`rate_request` is still never predicted on any seed. Its 3 test posts go to
`showcase` or `question`. That points back to the other half of my diagnosis,
too few examples, which more epochs can't fix.

Criterion 5 went from MET to MISSED. Now that the model predicts more than one
label, the confidence gap is a real test: on seed 42 the least-confident third
was still 86% accurate, so the gap was only 0.143. That makes the old MET
look like the artifact I suspected.

Accuracy now moves 0.13 across seeds, more than 10 points. With only 23 test
posts, one or two posts change the verdict, so the dataset is too small for a
stable measure.

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
