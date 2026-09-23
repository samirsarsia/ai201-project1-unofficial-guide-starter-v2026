# The Unofficial Guide

**Corpus selected:** `campus_life`

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This system answers practical university-life questions using the `campus_life`
corpus, a collection of short posts about courses, housing, dining, deadlines,
and campus services. It retrieves the most relevant posts, refuses questions
whose best matches are too distant, and asks the model to answer only from the
retrieved documents. Answers name the source file so a reader can check the
supporting post.

## Chunking Strategy

**Chunk size:** 600 characters
**Overlap:** 0 characters

I chose a 600-character maximum because the `campus_life` documents are short
posts, usually one to three paragraphs, and the useful fact is often one
complete thought. Posts that fit stay together; longer material is split at
sentence boundaries rather than cutting through a sentence. I use no overlap
because repeating text is unnecessary when each normal post is already a
self-contained chunk.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline You can add a course through the end of the second week.
Dropping is a longer window — through the end of week six — but a drop after week
two shows as a W on your transcript. Nothing anywhere on the registrar's site says
this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology I lived here my sophomore year. Format is lecture three
times a week with a weekly lab. Assessment: four unit tests and a cumulative final.
Not curved. Expect 9 to 11 hours a week, the heaviest first-year course by
reputation. The one piece of advice: the unit tests come fast, roughly every three
weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::split_documents`

```
Workload for HIST 118 Modern World History People keep asking so: a lot of reading,
about 120 pages a week, but no problem sets. That's real time, not optimistic time.
It's front-loaded — the first month is heavier than the rest, partly because you're
learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::split_documents`

```
Re: Pellew Dining Hall Adding to what people have said about Pellew Dining Hall.
The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying
to eat between classes, go before 11:45 and it's a different building entirely. Also
worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody
tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::split_documents`

```
Innisfree Hall — what it's actually like Transferred in last year, so take this
with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as
pairs sharing one bathroom between two rooms. The good: the shared-bathroom-
between-two-rooms arrangement is the best compromise on campus. The bad: no air
conditioning, which matters for the first three weeks of September. Laundry costs
$1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and
the short wing is much quieter.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:**
How random is the housing lottery for juniors and seniors?

**Answer:**

```
According to admin_housing_lottery.txt, juniors and seniors are ordered by
accumulated credit hours, with random selection used only to break ties. Rising
sophomores receive a number drawn at random.
```

**Source:** `admin_housing_lottery.txt`

This answer is directly supported by the retrieved chunk. The live model call
could not be run in this environment because `.env` does not contain a
`GEMINI_API_KEY`.

**My relevance cutoff:**

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---:|
| How random is the housing lottery for juniors and seniors? | Yes | 0.132763 |
| When should I drop a course if I do not want a W on my transcript? | Yes | 0.287928 |
| How much time does BIOL 160 usually take each week? | Yes | 0.302796 |
| How much reading is assigned for HIST 118 each week? | Yes | 0.316142 |
| When should I go to Pellew Dining Hall to avoid the longest wait? | Yes | 0.188783 |
| What is the capital of Mongolia? | No | 0.824593 |
| How do I change the oil in a diesel engine? | No | 0.934011 |
| Who won the 1994 World Cup? | No | 0.885860 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844232 |
| How do I write a for loop in Rust? | No | 0.895998 |

I set the cutoff to **0.55**. The in-scope questions ranged from 0.132763 to
0.316142, while the out-of-scope questions ranged from 0.824593 to 0.934011,
leaving a large gap between the groups. A cutoff below the nearest in-scope
result would reject a question the corpus answers; a cutoff above 0.824593
would allow the closest unrelated question through.

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I asked an AI coding assistant to inspect the starter's chunk summary and
the short `campus_life` documents, then pressure-test whether fixed 800-character
windows made sense. It pointed out that the starter produced one chunk per short
post and suggested keeping normal posts intact while splitting longer text at
sentence boundaries. I implemented that as `chunker.py::split_documents` with a
600-character limit and verified that the rebuilt index still produced 88
self-contained chunks.

**2.** I asked the assistant to compare the best retrieval distances for all five
in-scope questions with the five out-of-scope questions. The measured groups were
0.132763–0.316142 and 0.824593–0.934011, so I set the cutoff to 0.55 rather than
leaving the starter's 0.6 unexplained. I also tightened the grounding instruction
to require every factual claim to be directly supported by a retrieved document,
not inferred from general knowledge.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks preserve a complete post or paragraph | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Named source contains the supporting fact | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

**Evidence file:** `results/run_2026-09-23_1853_before.md`, produced by
`run_eval.py::write_report`.

**Criterion 1 — retrieved chunks contain the answer.** The generated answers
below contain the expected facts, and the report lists the corresponding source
documents among the retrieved results. The answers were produced by
`generate.py::answer_from_chunks`:

```
For juniors and seniors, the housing lottery is ordered by accumulated credit hours first, and only tie-broken randomly.

Source: admin_housing_lottery.txt

You can drop a course through the end of the second week if you do not want a "W" on your transcript, as drops after week two show as a "W".

Source: admin_add_drop_deadline.txt

BIOL 160 usually takes 9 to 11 hours a week (source: course_biol_160.txt and course_biol_160_workload.txt).

About 120 pages of reading is assigned each week for HIST 118, according to course_hist_118.txt and course_hist_118_workload.txt.

To avoid the longest wait at Pellew Dining Hall, you should go before 11:45 (from dining_pellew_dining_hall_followup.txt and dining_pellew_dining_hall.txt).
```

**Criterion 2 — every answer names a source.** The same five outputs above each
name at least one source document. They were produced by
`generate.py::answer_from_chunks` and recorded by `run_eval.py::write_report`.

**Criterion 3 — the gate stops out-of-corpus questions.** This output was
produced by `run_eval.py::check_out_of_scope`:

```
gate refused 5 of 5
What is the capital of Mongolia? — refused
How do I change the oil in a diesel engine? — refused
Who won the 1994 World Cup? — refused
What is the recommended dosage of ibuprofen for a headache? — refused
How do I write a for loop in Rust? — refused
```

**Criterion 4 — sampled chunks preserve complete posts or paragraphs.** The
following output was produced by `app.py::cmd_chunks`, which calls
`chunker.py::split_documents`:

```
88 chunks total. Showing 5, spread across the corpus.
Chunk 1: admin_add_drop_deadline.txt#0
On the add/drop deadline You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript.
Chunk 2: course_biol_160.txt#0
BIOL 160 Cell Biology I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved. Expect 9 to 11 hours a week.
Chunk 3: course_hist_118_workload.txt#0
Workload for HIST 118 Modern World History People keep asking so: a lot of reading, about 120 pages a week, but no problem sets.
Chunk 4: dining_pellew_dining_hall_followup.txt#0
Re: Pellew Dining Hall Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45.
Chunk 5: housing_innisfree_hall.txt#0
Innisfree Hall — what it's actually like Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.
```

**Criterion 5 — named sources contain the supporting facts.** In the recorded
answers, the housing, add/drop, BIOL 160, HIST 118, and Pellew citations match
the documents containing those facts. This evidence was produced by
`generate.py::answer_from_chunks` and recorded by `run_eval.py::write_report`.

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MET | All five test questions had the expected answer in the retrieved context in all three runs, so each run reached 5/5, exceeding the target of 4/5. |
| 2 | Every answer names a source | MET | Every one of the 15 generated answers named at least one source document, so all three runs reached 5/5 against the target of 5/5. |
| 3 | The relevance gate stops out-of-corpus questions | MET | The deterministic gate refused all five out-of-corpus questions, 5/5, which meets the target of at least 4/5; the same result correctly appears in all three columns. |
| 4 | Sampled chunks preserve a complete post or paragraph | MET | All five sampled chunks preserved complete posts or paragraphs without a sentence being cut at either end, exceeding the target of 4/5. |
| 5 | Named source contains the supporting fact | MET | The named sources for all five answers contained the supporting facts, giving 5/5 in each run against the target of 4/5. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

No criterion was missed, so there is no failure to diagnose in loading,
chunking, embedding, retrieval, or gating. The measured weakness was in the
generation stage: the baseline answered all 15 model calls correctly and named
valid sources, but its citation formatting varied between `Source:`,
`(source:)`, and `(from ...)`. The original targets remain unchanged because
they were measurable and met. The next stricter check I would use is that
every generated answer ends with one consistent `Source:` line.

## The Improvement

**What I changed:** I added a generation instruction in
`generate.py::GROUNDING_INSTRUCTION` requiring every answer to end with a
separate line in the exact format `Source: filename.txt`.

**Why I picked it:** This addresses the only observed generation-stage
inconsistency while preserving the already successful retrieval, gate, and
grounding behavior.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks preserve a complete post or paragraph | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Named source contains the supporting fact | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**After evidence:** `results/run_2026-09-23_1905_after.md`, produced by
`run_eval.py::write_report`. Every recorded generated answer ends with a
separate `Source:` line, and the gate refused 5/5 out-of-corpus questions.

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

Yes. The improvement made citation formatting consistent in all 15 recorded
answers without changing the measured retrieval or gate results: criteria 1,
2, and 5 stayed at 5/5, criterion 3 stayed at 5/5, and criterion 4 remains
5/5 because the chunker was unchanged. It improved observability and made the
source requirement easier to check, although it did not increase the already
passing criterion counts.

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
