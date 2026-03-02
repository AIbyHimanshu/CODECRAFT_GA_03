# CODECRAFT_GA_03 — Text Generation with Markov Chains
> A statistical text generation model that predicts the probability of a character or word based on the previous one(s), implemented using N-gram Markov Chains in Python.

---

## Overview

This project implements a **Markov Chain-based text generator** from scratch using only Python's standard library. Given a training corpus, the model learns transition probabilities between tokens (words or characters) and uses weighted random sampling to generate new, statistically plausible text.

The key idea: the next token depends only on the previous `N` tokens — this is the **Markov property**.

---

## How It Works

```
Corpus → Tokenize → Build Transition Table → Weighted Sampling → Generated Text
```

| Step | Description |
|------|-------------|
| **Tokenize** | Split corpus into word tokens or characters |
| **Build Chain** | Map every N-gram state → possible next tokens with counts |
| **Weighted Pick** | Sample next token proportional to observed frequency |
| **Generate** | Walk the chain for `length` steps, updating state each time |
| **Detokenize** | Join tokens back into readable text (fix punctuation spacing) |

### Transition Table Example (Order 2, Word-level)

```
('to', 'be')     →  'or'(1), 'that'(1), 'wished'(1)
('is', 'the')    →  'question'(1), 'respect'(1)
('to', 'sleep')  →  'no'(2), 'perchance'(1)
```

---

## Project Structure

```
CODECRAFT_GA_03/
│
├── GA_03.ipynb      # Main Colab notebook
└── README.md               # This file
```

---

## Getting Started

### Run in Google Colab

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

1. Open `GA_03.ipynb` in Google Colab
2. Run all cells (`Runtime → Run All`)
3. No external dependencies required — uses only Python standard library

---

## Core Functions

### `build_chain(tokens, order=2)`
Builds the N-gram transition table.
```python
chain = build_chain(word_tokenize(corpus), order=2)
# Returns: {('to', 'be'): {'or': 1, 'that': 1, 'wished': 1}, ...}
```

### `generate_text(chain, order, length=80, seed=None)`
Generates a sequence of tokens by walking the chain.
```python
tokens = generate_text(chain, order=2, length=60, seed=('to', 'be'))
```

### `tokens_to_text(tokens, mode='word')`
Converts token list to a clean readable string.
```python
text = tokens_to_text(tokens, mode='word')
# Fixes punctuation spacing: "question , tis" → "question, tis"
```

---

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `order` | int | 2 | N-gram size. Higher = more coherent, less creative |
| `length` | int | 80 | Number of tokens to generate |
| `mode` | str | `'word'` | `'word'` for word-level, `'char'` for character-level |
| `seed` | tuple | `None` | Optional starting state (must exist in chain) |

---

## Sample Output

### Word-level (Order 2) — Shakespeare Corpus
```
against a sea of troubles and by a sleep to say we end the heartache
and the thousand natural shocks that flesh is heir to tis a consummation
devoutly to be wished to die to sleep perchance to dream...
```

### Character-level (Order 4) — Same Corpus
```
ust give us pause the slings and natural shocks that is heir to takes
calamity of troubles and the pangs and scorns of outrageous fortune
or to sleep no mortal coil must give us pause...
```

### Order Comparison (Word-level)

| Order | Coherence | Diversity | Sample |
|-------|-----------|-----------|--------|
| 1 | Low | High | *"mind to be or to tis a sleep no more and the unworthy..."* |
| 2 | Medium | Medium | *"takes when he himself might his quietus make with a bare bodkin..."* |
| 3 | High | Low | *"take arms against a sea of troubles and by opposing end them..."* |

---

## Design Decisions

**Why `defaultdict(lambda: defaultdict(int))` for the chain?**  
Auto-initializes nested dicts cleanly — no `KeyError` when inserting new states.

**Why `random.choices` for sampling?**  
Natively supports weighted sampling by frequency counts in one line — no manual normalization needed.

**Dead-end handling:**  
When generation hits a state not in the chain (can happen with high-order chains on small corpora), the generator picks a new random valid state and continues — without duplicating tokens into the output.

**Punctuation detokenization:**  
`re.sub(r"\s+([.,!?;:])", r"\1", text)` removes the space that `' '.join()` would insert before punctuation marks.

---

## Key Concepts

- **Markov Property** — future state depends only on the current state, not the full history
- **N-gram Order** — controls how much context the model uses
- **Transition Probability** — `P(next | state) = count(state → next) / sum(all transitions from state)`
- **Sparse vs Dense chains** — small corpora + high order = many dead ends; large corpora + low order = smooth generation

---

## References

- [Text Generation with Markov Chains — Towards Data Science](https://towardsdatascience.com/text-generation-with-markov-chains-an-introduction-to-using-markovify-742e6680dc33/)
- [Predictive Text and Text Generation — Allison Parrish (GitHub)](https://github.com/aparrish/predictive-text-and-text-generation/blob/master/predictive-text-and-text-generation.ipynb)

---
