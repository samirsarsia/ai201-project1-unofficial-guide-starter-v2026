# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
The campus_life documents are short posts, and the baseline index keeps each
post in one chunk, so the answer should be present in the retrieved context for
most specific questions. I am allowing one miss because closely related posts
may compete for the same retrieval slots.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
The source filename is attached to every stored chunk and the answer prompt
asks the model to cite its sources, so all five answers should be able to name
the document that supplied the fact.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
The out-of-scope questions concern unrelated subjects such as sports, medicine,
and programming, while this corpus is about university life. Their retrieved
distances should therefore be separated from the in-corpus questions enough
for the existing 0.6 cutoff to refuse at least four of five.

---

## 4. Something about your chunks

At least 4 of 5 sampled campus_life chunks should preserve a complete post or
complete paragraph, without cutting a sentence in half at either end.



**Why this target:**
Most campus_life documents are only one to three paragraphs, and the baseline
already produces one chunk per document. Keeping complete thoughts together is
more useful here than making many smaller fixed-size fragments.



---

## 5. Your choice

For at least 4 of 5 questions, the named source should be one of the documents
that actually contains the supporting fact, rather than merely a related source
returned by retrieval.



**Why this target:**
The corpus contains many similarly named topics, such as housing, dining, and
course posts, so correct source attribution matters more than simply displaying
a non-empty citation list.



---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
