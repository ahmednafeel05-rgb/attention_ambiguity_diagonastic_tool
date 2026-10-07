# Who is "it"?

Watching attention resolve pronoun ambiguity in GPT-2, token by token.

> "The knife cut the bread easily because **it** was so *sharp*." Change *sharp* to *soft* and "it" means the bread.
> GPT-2 XL has one attention head, **layer 20 head 4**, that moves with that word: 74% of its attention lands on *knife*, then 97% on *bread*.
> GPT-2 small has no head that does this reliably.

**Live site:** `https://<your-username>.github.io/<repo-name>/` (see "Publish the site" below)

## What's in this repo

| Path | What it is |
|---|---|
| `docs/index.html` | The interactive site. Pick a sentence pair, switch the clue word, pick a model, and see how each head's attention moves. |
| `docs/attention_data.json` | Real attention weights exported from GPT-2 small and GPT-2 XL for 14 sentence pairs (28 sentences). |
| `notebook/attention_analysis.ipynb` | The Colab notebook that finds the heads, runs the held-out test and exports the data. |
| `WRITEUP.md` | A short write-up of three sentence pairs and what the attention showed. |

## Main results

| | GPT-2 small | GPT-2 XL |
|---|---|---|
| Pairs answered correctly in both versions | 3 / 14 | 6 / 14 |
| Best head's average swing toward the correct noun | +0.04 | **+0.52** (L20H4) |
| Pairs where that head swings the right way | – | 13 / 14 |

*Swing* = (attention to noun A − noun B with the first clue) − (the same with the second clue). It runs from −2 to +2. Positive means the head moves toward the correct noun each time.

## Method

1. **Models:** `gpt2` (12 layers × 12 heads) and `gpt2-xl` (48 layers × 25 heads), loaded with Hugging Face Transformers using `attn_implementation="eager"` and `output_attentions=True`.
2. **Sentences:** 14 Winograd-style pairs. Each pair is two sentences that differ by one clue word, and that word decides what the pronoun refers to.
3. **What the model answers:** "it" is replaced by each noun in turn. The sentence with the higher log-likelihood is the model's answer.
4. **Where attention is read:** from the final clue word. The word "it" is the wrong place, because GPT-2 uses masked (left-to-right) attention: at "it", both versions of the sentence are identical, and we confirmed the attention difference there is exactly 0.0.
5. **Finding heads:** the 1,200 XL heads were ranked on 2 pairs (man/son, fish/worm). The top heads were then tested on 9 held-out pairs.
6. **Held-out result:** on held-out pairs XL solved, the top 5 heads swung +0.389 on average, against +0.010 for a typical head. On pairs XL failed, they swung +0.087. L20H4 generalised; L14H24 did not.

## Limits

- 14 pairs is a small set, and the sentences were written by hand.
- The site shows precomputed sentences rather than letting you type your own, because GPT-2 XL needs a GPU.
- Attention shows where a head looks, not why the model decides. Proving that L20H4 *causes* the answer would need ablation or activation patching.

## Publish the site (GitHub Pages)

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and the `/docs` folder, then **Save**.
4. After a minute, the site is live at `https://<your-username>.github.io/<repo-name>/`.

To preview locally: `cd docs && python -m http.server`, then open http://localhost:8000. Opening `index.html` directly from disk won't load the data.

## Reproduce

Open `notebook/attention_analysis.ipynb` in Google Colab, choose **Runtime → Change runtime type → T4 GPU**, and run all cells. The last cell writes `attention_data.json`; copy it into `docs/`.
