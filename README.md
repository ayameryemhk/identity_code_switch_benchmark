# Evaluating LLM Sensitivity to Pragmatic & Identity-Marker Code-Switching
### (Algerian Arabic–French–English)

A benchmark and evaluation harness for testing whether large language models
(GPT-4o, Claude, Mistral, Llama, etc.) can interpret the **pragmatic and
identity-driven functions** of Arabic–French–English code-switching, rather
than defaulting to flat, literal translation that erases the sociolinguistic
signal.

---

## 1. Problem Framing

Commercial translation and conversational AI systems are typically optimized
for propositional accuracy: they aim to preserve *what was said*, not
*why the speaker chose to say it in that language*. This is a real gap for
Maghrebi (and broadly diglossic/multilingual) speech communities, where the
**choice of code** carries as much meaning as the words themselves.

In Algeria, bilingual and trilingual speakers routinely alternate between
Darija (Algerian Arabic), French, and increasingly English within a single
conversational turn. This is not random or purely lexical — it is a resource
speakers use to **index a stance**: to sound warmer, to soften a criticism,
to mark an opinion as personal rather than collective, to be sarcastic, or to
defend a position. A model that "translates" these switches into a single
flat register collapses exactly the information a fluent human listener
would pick up on instantly.

This repository is built around the empirical findings of a semi-ethnographic
sociolinguistic study of eight Algerian bilingual speakers, which documented
that switching toward English in particular functions as an **identity
marker**: participants associated English with positivity, politeness,
individuality, an authentic/humorous self-presentation, and defensiveness —
largely because English is perceived to belong to an individualistic
cultural register, in contrast to the more collectivist orientation
associated with Arabic in these interactions.

**The benchmark asks a simple question:** when an LLM is given a
code-switched excerpt, does its interpretation surface *that* stance layer,
or does it just produce a literal, stance-blind translation?

---

## 2. Research Basis & Stance Taxonomy

The dataset schema and failure taxonomy are grounded in five stance
functions identified for English code-switching among Algerian bilinguals:

| # | Stance Function | What It Looks Like | What a Literal Model Misses |
|---|---|---|---|
| 1 | **Indexing Positivity** | Switching to English to express excitement, joy, or enthusiasm ("Cool!", "Amazing!") | The emotional lift / solidarity signal; treats it as a neutral interjection |
| 2 | **Politeness / Reducing Directness** | Switching to English to soften a criticism or disagreement that would sound harsh in Arabic | The mitigation function; renders the utterance as bluntly critical |
| 3 | **Expressing Individual Concerns** | English used with "I believe", "I think", "I mind my own business" to mark a personally-held (vs. group) view, pushing against a collectivist norm | The individualistic framing; defaults to generic or collective phrasing |
| 4 | **Authentic / Sarcastic Identity** | English used for banter, teasing, or dry humor, often borrowing from Anglophone pop-culture register | The sarcasm; interprets the line at face value |
| 5 | **Defensive Strategy** | Switching to English to assert or hold a position under social pressure | The defensive/assertive footing; reads it as a plain factual claim |

These five categories are encoded directly as the `pragmatic_intent` /
`target_stance` labels in `data/identity_codeswitch_bench.csv`, together with
an `llm_failure_mode` tag describing the most likely way a model gets it
wrong (`Literal Loss of Softness`, `Misattributing Speaker Tone`,
`Failure to Capture Sarcasm`).

---

## 3. Repository Structure

```
.
├── README.md
├── requirements.txt
├── data/
│   └── identity_codeswitch_bench.csv    # 15 parallel benchmark samples
├── src/
│   └── evaluate_pragmatic_llm.py        # scoring / reporting harness
├── tests/
│   └── test_evaluate_pragmatic_llm.py   # unit tests for scorers
└── reports/
    └── demo_report.json                  # example output (naive baseline)
```

### `data/identity_codeswitch_bench.csv` schema

| Column | Description |
|---|---|
| `sample_id` | Unique ID, e.g. `CS_IDENTITY_01` |
| `source_dialogue` | Code-switched conversational excerpt (Darija ↔ French/English) |
| `literal_translation` | Plain literal English translation — stands in for a naive MT baseline |
| `pragmatic_intent` | True underlying sociolinguistic function of the switch |
| `target_stance` | Primary cultural/psychological stance the switch indexes |
| `llm_failure_mode` | Expected failure category if a model misses the pragmatic layer |

The 15 seed samples are original, synthetically constructed dialogues
modeled on the stance categories above — written for this benchmark rather
than reproduced from any transcript, so the dataset can be freely extended,
relicensed, and shared. Contributions of new samples (see §7) are welcome.

---

## 4. Source Code

`src/evaluate_pragmatic_llm.py` implements:

- **`semantic_similarity(a, b)`** — TF-IDF cosine similarity between a
  model's output and the literal reference (dependency-light, no model
  download required; swap in your own sentence-embedding model for a
  stronger signal).
- **`PragmaticErrorScorer`** — a custom pragmatic error taxonomy scorer that
  computes the percentage of instances where a model's explanation misses
  the stance/identity tag versus defaulting to literal translation
  (`pragmatic_accuracy_pct` vs. `literal_translation_rate_pct`).
- **`analyze_individualistic_stance()` / `stance_orientation()`** — a stance
  analysis utility that checks whether a model's English explanation
  correctly surfaces individualistic markers (*"I believe"*, *"I mind my own
  business"*) versus defaulting to generic collective phrasing.
- **`evaluate()` / `write_report()`** — automated failure reporting that
  produces a structured JSON or CSV report broken down by stance category
  and failure mode.

---

## 5. Project / Portfolio Note

This repository is offered as an open-access evaluation suite at the
intersection of **computational sociolinguistics**, **low-resource NLP**,
and **pragmatic alignment evaluation** for LLMs. It demonstrates an
end-to-end workflow — from operationalizing qualitative sociolinguistic
findings into a labeled benchmark schema, to building a lightweight,
dependency-conscious scoring harness, to unit-testing the metrics — aimed at
surfacing a class of LLM failure (identity-marker code-switching
insensitivity) that standard MT/BLEU-style evaluation does not capture.

---

## 6. Citation & Acknowledgment

The stance taxonomy and empirical motivation for this benchmark draw on:

> El Ouali, F. Z. (2022). Code Switching as an Identity Marker among
> Algerian Bilinguals: A Sociolinguistic Investigation of Arabic-English
> Speakers. *Literatures and Languages Journal*, 22(1), 495–506.




