# Who is "it"? Three sentences, read through GPT-2's attention

A pronoun like "it" often has two possible meanings, and one later word decides which one is right. This write-up looks at three such sentence pairs. For each, it shows what GPT-2 small and GPT-2 XL answered, and where one attention head in XL, **layer 20 head 4 (L20H4)**, looked.

**How to read the numbers.** Attention is measured from the final clue word, the first point where the model has seen it. Each percentage is the share of L20H4's attention that lands on a noun; one head's shares add up to 100% across the sentence. *Swing* is how far the head moves toward the correct noun when the clue word changes. It runs from −2 to +2.

## 1. Trophy and suitcase: both models fail

> The trophy didn't fit in the suitcase because it was too **big** / **small**.

| | big (answer: trophy) | small (answer: suitcase) |
|---|---|---|
| GPT-2 small answers | suitcase ✗ | suitcase ✓ |
| GPT-2 XL answers | suitcase ✗ | suitcase ✓ |
| L20H4 → trophy | 3% | 2% |
| L20H4 → suitcase | 50% | 61% |

**What it showed.** Neither model uses the clue. Both pick "suitcase", the noun closest to "it", for both versions, and L20H4 looks at the suitcase both times (swing +0.12).

This pair also shows why attention must be read from the clue word. At the word "it", the attention in the two sentences is **identical** (difference 0.0). GPT-2 reads left to right, so at "it" it has not seen "big" or "small" yet. A visualizer that only shows attention from the pronoun can never show this kind of ambiguity being resolved.

## 2. Knife and bread: the head resolves it

> The knife cut the bread easily because it was so **sharp** / **soft**.

| | sharp (answer: knife) | soft (answer: bread) |
|---|---|---|
| GPT-2 XL answers | knife ✓ | bread ✓ |
| L20H4 → knife | **74%** | 0% |
| L20H4 → bread | 6% | **97%** |

**What it showed.** One word changes and L20H4's attention moves almost completely from one noun to the other, a swing of **+1.65**. This pair was held out: L20H4 was chosen using two other sentence pairs, so this result is a test, not a fit.

GPT-2 small also answers this pair correctly, so it's an easier case. "Sharp" goes with knives so often that a small model can manage it. Across all 14 pairs, though, small resolves only 3, and no head in small swings consistently. Its best head averages +0.04, against +0.52 for L20H4.

## 3. Lion and deer: the head moves, but never switches

> The lion caught the deer because it was so **fast** / **slow**.

| | fast (answer: lion) | slow (answer: deer) |
|---|---|---|
| GPT-2 XL answers | deer ✗ | deer ✓ |
| L20H4 → lion | 24% | 4% |
| L20H4 → deer | 52% | 88% |

**What it showed.** L20H4 moves the right way when "slow" becomes "fast": lion rises from 4% to 24%, and deer falls from 88% to 52% (swing +0.56). But it never crosses over, and the model's answer doesn't either. It says "deer" both times.

So a positive swing alone doesn't mean the ambiguity is resolved. What matters is whether the head ends up on the right noun. Truck/bus shows the same pattern (swing +0.71, XL still wrong).

## Summary

| | GPT-2 small | GPT-2 XL |
|---|---|---|
| Pairs answered correctly in both versions | 3 / 14 | 6 / 14 |
| Best head's average swing | +0.04 | +0.52 (L20H4) |
| Pairs where L20H4 swings the right way | – | 13 / 14 |

- The ability to resolve these pronouns appears with scale, inside the same model family.
- In XL, it is visible in one middle-layer head (layer 20 of 48), which held up on 9 held-out pairs. Another candidate, L14H24, did not.
- **Limits:** 14 pairs is small, and attention shows *where* a head looks, not *why* the model decides. Testing whether L20H4 causes the answer would need ablation or activation patching.
