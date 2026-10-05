# How reliable is a small LLM as a pairwise judge? Position bias, length bias, and self-uncertainty

Judge: Qwen2.5-7B-Instruct-AWQ (zero-shot, A/B logprobs). Data: MT-Bench human judgments (2,396 unique pairs; 1,858 with a non-tied human majority).

## Findings (95% bootstrap CIs over pairs)

| Measure | Result |
|---|---|
| Agreement with humans, single order | 0.791 [0.772, 0.809] |
| Both orders, averaged probability | 0.811 [0.793, 0.828] |
| Paired gain from averaging | +0.020 [+0.008, +0.032] |
| Both orders, tie if verdicts disagree | 0.788 overall; 0.847 on the 83% of pairs it decides |
| Verdict flips when answer order is swapped | 19.3% [17.8, 20.9] |
| Picks the first-shown answer | 0.479 [0.470, 0.487] |
| Picks the longer answer (length-skewed pairs, n=1,368): judge vs humans | 0.839 vs 0.757, difference +0.083 [+0.064, +0.102] |

- Position bias shows up mainly as instability (about 1 in 5 verdicts change with order), not as a fixed slot preference.
- On order-consistent pairs (n=1,541) the judge agrees with humans 84.7% of the time. On flipped pairs (n=317) a single order is at chance (0.517) and averaging gives 0.634.
- The judge's confidence predicts agreement with humans: keeping the most confident 100% / 60% / 20% of verdicts (by averaged-probability margin) gives accuracy 0.811 [0.793, 0.829] / 0.905 [0.889, 0.921] / 0.957 [0.935, 0.977]. The margin's AUROC for predicting agreement is 0.729 over all pairs and 0.72 within order-consistent pairs only, so it carries information beyond the order flip.
- When humans preferred the shorter answer, the judge agreed only 53% of the time (96% when humans preferred the longer one).

## Limitations
One judge model (7B, AWQ-quantized), one dataset, zero-shot prompting. Most pairs have one human vote, so label noise caps measurable accuracy and probably inflates the judge-human length gap. Multi-turn conversations are flattened into one prompt. Tied and split-vote pairs are excluded. Pairs share questions, so CIs are slightly optimistic. Not peer reviewed.

## Reproduce
`01_judge_bias_qwen7b.ipynb` (vLLM on Kaggle T4 x2, about 25 minutes of inference). Raw judgments: `judge_qwen7b.json`.
