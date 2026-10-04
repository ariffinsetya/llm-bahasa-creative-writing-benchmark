# Indonesian context-aware scene drafting benchmark

Date: 2026-10-04
Status: Agreed conversational design, formalized for written review

## Purpose and scope

Find models that can replace the author's current Gemini-based model in an Indonesian novel-writing workflow: develop informal scene notes into engaging prose while preserving voice, continuity, and the author's control over consequential story decisions.

The primary benchmark is context-aware scene drafting. A general Indonesian creative-writing benchmark is a later extension. Community publication is an intended future outcome, not an authorized publication action in this phase.

The user values natural dialogue, effective use of context, and restraint. Their experience with another model exposed a specific failure: an invented wartime death became recurring canon and began redirecting a political plot. This is motivation for the benchmark, not a general performance claim about a model family.

The author's preferences are the primary measure of workflow suitability. They must not be presented as a universal measure of literary quality.

## Task definition

Input consists of established prior prose, informal notes for the next scene, and a shared drafting instruction. Output is the developed scene in Bahasa Indonesia.

Preserve natural note-taking: slang, code-switching, dialogue fragments, explanatory asides, and incomplete sentences. Do not polish notes differently for different models. Prior prose and notes are source material; the shared instruction specifies the task.

Use this versioned default instruction:

> Kembangkan catatan adegan berikut menjadi prosa novel dalam Bahasa Indonesia. Pertahankan fakta yang sudah ditetapkan, sudut pandang, suara narator, karakterisasi, dan arah serta suasana adegan yang diminta. Kamu boleh menambahkan tindakan, detail suasana, dialog, dan kejadian kecil yang membantu adegan. Jangan menambahkan fakta besar yang mengubah latar belakang, hubungan, motivasi utama, atau arah konflik tanpa dasar dalam bahan yang diberikan atau izin eksplisit. Gunakan konteks sebelumnya secara selektif; jangan merangkum ulang seluruh cerita. Ikuti rentang panjang yang ditentukan. Keluarkan hanya teks adegan.

The instruction explicitly communicates the desired author control. Results therefore measure performance under that instruction, not behavior under every possible user prompt.

Default output range: 600–900 words per scene. Cases may override this before the suite is frozen; every model receives the same range for a given case. Count whitespace-separated words for reproducible length reporting. Output length is a diagnostic rather than a proxy for literary quality.

## Pilot dataset

Six original story setups, each with three successive scene briefs: 18 scenes. Run two independent repetitions per model. Include an exactly identified Gemini baseline and initially up to three alternatives that the connected OmniRoute instance can actually serve.

Recommended setup coverage:

| Setup | Main behavior tested |
|---|---|
| Domestic drama followed by workplace onboarding | Lighthearted tone after heavy context; a new woman does not imply romance |
| Political workplace or civic drama | Political tension without invented deaths, conspiracies, or secret affiliations |
| Family story with religious background | Contextual religious detail without sermonizing or stereotypes |
| Quiet friendship or everyday life | Preserve a low-stakes scene without forced revelations or cliffhangers |
| Speculative story with explicit world rules | Maintain continuity without inventing new powers or decisive lore |
| Ensemble workplace comedy | Distinct natural dialogue, professional competence, and Indonesian code-switching |

These are proposed dataset choices, not claims that cases have already been written. Each three-scene sequence establishes relationships, offers an opportunity for tempting but unsupported drama, and moves to a subsequent scene where persistence can be observed. Briefs must not contain traps that make good fiction impossible.

For each scene, annotate required events, intended tone, established facts with source passages, permissible local inventions, consequential additions requiring authorization, and any explicitly permitted new plot development. Keep annotations for evaluation out of generation prompts unless their content is part of the author's supplied instruction or notes.

Include positive opportunities for creative local elaboration. The benchmark must not reward dull literal transcription simply because it avoids inventions.

## Two evaluation tracks

### Fixed context

Every candidate receives identical author-provided prior prose and scene notes. For scenes two and three, use frozen author-written reference prose for earlier scenes. Never use one candidate's output as another candidate's fixed context.

This track supports fair same-input comparisons and isolates individual scene-drafting ability. It does not measure accumulation of a candidate's own inventions.

### Model-generated sequences

Scene one starts from the same setup context. Scenes two and three receive that candidate's preceding outputs plus the next shared scene brief. Repeat complete sequences independently; do not mix outputs across repetitions.

Keep the original author-provided facts and instruction available at every step. Fit all pilot histories within the smallest participating context capacity and do not silently summarize or truncate them. A configuration that cannot fit is ineligible for that sequence and is reported separately.

This track measures plot drift and propagation of unsupported canon. Inputs differ after the first scene, so report sequence results separately from fixed-context scores.

Scene one has identical input in both tracks. Reuse its output within the corresponding repetition, then branch into the two track histories. This gives 30 unique generations per repetition per model: six shared first scenes, 12 later fixed-context scenes, and 12 later sequence scenes. Two repetitions give 60 generations per model; four models give 240 pilot generations, excluding retries and judge calls.

## Invention policy and drift annotation

| Addition | Default policy | Example |
|---|---|---|
| Scene texture | Allowed if consistent | A gesture, room detail, pause, or passing sound |
| Local elaboration | Allowed if useful and low consequence | An interview joke or incidental conversation |
| Consequential story fact | Requires author support or permission | Secret marriage, death, hidden allegiance, a newly asserted romantic attraction |

Classify by consequence, not by how many words an addition takes. A single sentence can redirect the plot. A long exchange may remain harmless local elaboration. Explicitly requested plot development is allowed.

For every suspected deviation, record the output quotation, the relevant source or absence of support, severity, first appearance, later callbacks, and resulting changes to events or motivation. Use these labels:

- Minor deviation: unsupported addition or tonal mismatch requiring a local edit, without materially changing the story direction.
- Major deviation: unsupported fact, contradiction, or shift of scene intent that creates substantial revision work or changes consequential story direction.
- Persistent major deviation: an earlier major deviation is reused as established fact in a later scene.
- Plot redirection: a deviation begins determining later conflicts, choices, or outcomes.

These labels may co-occur. Report major deviations visibly alongside scores rather than hiding them in an overall average.

Established facts are grounded in author-supplied material. In the sequence track, a candidate's unsupported invention remains unsupported even after its own earlier output enters the context. Preserve provenance to avoid laundering inventions into canon.

Treat first-person accusations as narrator perspective unless author material establishes them as objective facts. Do not automatically penalize an unreliable narrator or demand that every scene show every character's perspective.

## Scoring

| Dimension | Weight | Evaluation focus |
|---|---:|---|
| Fidelity to scene intent and author control | 30% | Requested events, tone, and restraint on consequential additions |
| Natural Indonesian dialogue and narrative voice | 25% | Idiomatic phrasing, distinct voices, appropriate register and code-switching |
| Continuity and characterization | 20% | Established facts, relationships, motivations, viewpoint, and behavior |
| Scene craft | 15% | Actions, pacing, emotional progression, and purposeful use of context |
| Useful creative elaboration | 10% | Added detail enriches the scene without imposing unwanted commitments |

Rate each dimension from 1 to 5. Anchors:

- 1: fails the dimension substantially; requires extensive rewriting.
- 3: usable foundation with noticeable corrections or uneven execution.
- 5: strongly fulfills the dimension; requires only light editing.
- 2 and 4: intermediate performance between the adjacent anchors.

Before scoring the full pilot, develop dimension-specific Indonesian examples for anchors 1, 3, and 5 and calibrate them on a small sample. Freeze rubric version and examples for the evaluated run.

Calculate a 0–100 score as 25 × (weighted mean rating − 1), using weights as fractions summing to one. Average equally across cases within each track, with repetitions averaged within case first. Also publish dimension and setup-level results. Ratings are ordinal judgments; the aggregate is a practical summary, not an objective measurement of literary merit.

Track exact constraint failures, length, unexpected English, repetitions, refusals, and truncation separately. Workplace English, intentional slang, and colloquial dialogue are not automatically errors. An introverted character being socially capable is not automatically inconsistent.

## Primary decision: blind comparison against baseline

For fixed-context cases, compare the baseline and a candidate on identical inputs and matching repetition index. Hide model identity and randomize display order; preserve the prose itself. The author chooses A, B, tie, or neither in response to:

> Versi mana yang akan kamu pakai sebagai dasar draf berikutnya, dengan kebutuhan penulisan ulang substantif yang lebih sedikit?

Record brief reasons and a usable/unusable judgment for each output. For sequence cases, compare complete three-scene bundles on the same question, so persistent drift remains visible.

Report wins, losses, ties, neither, usable-output rate, and deviation counts separately. If a preference index is used, compute (wins + 0.5 × ties) / (wins + losses + ties); exclude neither and display its count. Do not interpret a high index as adequate quality when usability is poor.

The author supplies the pilot's decisive judgments. Automated judges are optional assistance for rubric scoring and deviation triage; their agreement with author judgments must be measured before treating them as substitutes. Avoid using a candidate as its own sole judge. When multiple automated judges are used, preserve disagreements and review consequential flags with a human.

Use a small repeated, order-swapped sample to check preference consistency. With only six setups, results are exploratory; display counts and avoid confident claims from small numerical differences. Any uncertainty analysis must resample whole story setups, preserving correlated scenes and repetitions.

## OmniRoute execution contract

Use an OpenAI-compatible client and explicit configured provider/model routes. Discover actual configured models from the user's instance rather than assuming advertised free availability. The exact baseline model version must be identified before running comparisons.

Disable cross-model fallback, prompt compression, automatic prompt rewriting, and response-cache reuse for repetitions. Same-model retry or account switching must be logged. Record requested model, reported served identity, provider, route metadata when available, and the OmniRoute version/configuration. Provider-reported names are not independent proof of model identity; mark identity as verified by available routing evidence, reported only, or unknown. Exclude unknown identity from model-specific conclusions.

Use one frozen generation configuration per comparison group. Proposed pilot setting: temperature 0.8 where supported, default top-p and penalties, and a completion budget large enough for the requested range. Log effective settings and unsupported or ignored parameters; temperature values are not guaranteed to behave identically across models. Freeze the selected completion budget after capability checks. Record reasoning mode and whether hidden reasoning shares the completion budget.

Run sequentially initially, interleaving model order across cases to reduce time-of-day and quota effects. Preserve every attempt, including errors. Retry transient failures up to three total attempts with bounded backoff and respect provider retry guidance. Quota exhaustion pauses or defers work; never substitutes a different model or accepts an unknown paid fallback. Resume by case, track, repetition, and model configuration identifier.

Select the first completed valid response under the frozen retry policy rather than the most appealing response. Do not retry solely for poor prose, refusal, or an unwanted invention. Preserve partial/truncated output and its completion status.

Report transport failures, quota limitations, refusals, latency, truncation, token usage where supplied, and cost basis separately. Quality summaries must include scored sample counts and excluded attempts. Free marginal usage and access through an existing paid subscription are distinct cost categories.

## Components and data flow

The future implementation needs four small components:

1. Versioned dataset: contexts, notes, references, annotations, and source permissions.
2. Runner: prompt assembly, explicit model calls, resumable execution, and raw attempt storage.
3. Evaluation: anonymous comparisons, preference collection, rubric scores, and drift provenance.
4. Reports: separate track results, baseline comparisons, examples, and operational metrics.

Flow: frozen dataset and run manifest → model attempts → immutable outputs → anonymized evaluation → attributed reports. Keep author annotations out of generated messages and model identity out of judge-facing comparisons. Store credentials outside datasets and public artifacts.

Use local files for the first version; a hosted service or database is unnecessary. Dataset, prompt, rubric, model configuration, and output hashes must permit a report to be traced to its inputs. Raw outputs remain immutable; annotations and evaluations carry their own versions.

## Verification and completion criteria

Before a pilot, verify prompt equality for fixed-context cases, correct sequence isolation, shared first-scene reuse, retry/resume accounting, score calculation, and removal of identity from evaluation views. Capability checks must confirm routing attribution, input preservation, and sufficient context/completion capacity.

The first benchmark is complete when six setups and their 18 annotated briefs exist; the rubric has calibrated examples; the baseline and selected candidates have complete or explicitly accounted-for runs; the author has blind comparisons; and reports show preferences, dimension scores, major deviations, persistent drift, and availability separately.

There is no requirement that an alternative beat Gemini. A justified finding that no tested candidate is a suitable replacement is a valid outcome.

## Community extension

Design cases as original, fictionalized material suitable for eventual sharing. Do not publish private autobiographical details or the user's supplied prose without their authorization. Track authorship and licensing so permission can be resolved before release.

After validating the core workflow benchmark, extend to standalone stories, revision, constrained fiction, and other genres. Recruit additional Indonesian readers, report agreement and preference variation, retain held-out cases, and distinguish the author's replacement benchmark from broader community results. License selection and actual publication belong to that later phase.

## Operational inputs needed before a live run

- Exact current Gemini route/version and any generation settings the author wishes to reproduce.
- OmniRoute instance URL, authentication supplied securely, and available configured routes.
- The initial candidate shortlist after capability discovery.

These do not block dataset design or local runner planning. No model endpoint has been called, no dataset has been authored, and no benchmark result is claimed by this specification.

## Documentation reference

OmniRoute's upstream documentation was consulted during design for OpenAI-compatible access, routing, fallback, compression, and provider-dependent free access: https://github.com/diegosouzapw/OmniRoute and https://omniroute.hagicode.com/en-US/getting-started/quick-start/. Exact switches and metadata fields must be verified against the user's installed version during implementation.
