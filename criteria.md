# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy of at least 0.75 on the held-out test set

On every one of the three seeds, the trained model labels at least 75% of the test posts correctly.

**Why this target:** `showcase` is 57% of my data, so a model that answered `showcase` every time would already score about 0.57, and chance with three labels is 0.33. 0.75 means getting roughly 4 out of 5 right, well above
just guessing the biggest label. My labels depend on whether the post asks for something, which usually shows in the wording ("rate my", "how do I", "?"), so I'd expect a fine-tuned model to pick that up. I didn't go higher because my test split is only about 23–30 posts, so each mistake costs 3–4 points.

---

## 2. `showcase` and `question` each have an F1 of at least 0.75

On every seed, the F1 for `showcase` is at least 0.75 and the F1 for `question` is at least 0.75.

**Why this target:** These are my two big labels (85 and 48 posts), so each gets roughly 7–13 posts in the test split. That's enough for the score to mean something. They also have the clearest cues: `question` posts almost always
contain a question mark or "how do I / any advice", and `showcase` posts are a dish name with no request. If the model can't separate these two, it hasn't learned my taxonomy at all.

---

## 3. `rate_request` has an F1 of at least 0.40

On every seed, the F1 for `rate_request` is at least 0.40.

**Why this target:** `rate_request` is my smallest label (17 posts when I wrote this, about 11%), which leaves only about 3 in the test split. One post right or wrong swings its F1 by more than 0.20, so a strict target here would mostly measure luck. 0.40 is looser than for my other labels on purpose. It still rules out a model that never predicts `rate_request`, which would score 0. It's also my hardest label, because it sits on my hardest boundary:
a `showcase` post with self-criticism looks a lot like a `rate_request` without the explicit ask.

---

## 4. Every label is at least 20% of `labels.csv`, and none is above 60%

In the final `labels.csv` I train on, each of `showcase`, `question` and `rate_request` makes up between 20% and 60% of the rows.

**Why this target:** The brief caps any label at 70%, but my data at 150 rows is already 57% `showcase` and only 11% `rate_request`. That imbalance is the most likely reason for criterion 3 to fail. 20% of 200 rows is 40 posts, which
puts about 6 `rate_request` posts in the test split instead of 3. 60% keeps `showcase` from growing any further while I collect the last posts.

---

## 5. The most-confident third is at least 0.20 more accurate than the least-confident third

On every seed, the accuracy on the model's most-confident third of test predictions is at least 0.20 higher than on its least-confident third (from the notebook's confidence report).

**Why this target:** Many of my posts are clear-cut: "Rate my dish" or a dish name with a recipe. A smaller set is borderline, like "Am I cooked chat?" or self-critique followed by "open to tips". If the model has learned my labels, it should be sure about the clear-cut posts and unsure about the borderline ones, and its accuracy should reflect that. With about 8 posts per third, 0.20 is
roughly 2 posts, the smallest gap I'd trust as more than noise.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
