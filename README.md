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

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

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

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



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
