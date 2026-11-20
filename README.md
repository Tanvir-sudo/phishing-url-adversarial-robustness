# Adversarial Robustness, Generalization, and Explanation Stability in URL-Based Phishing Detection

Code for the paper:

> **T. Ahamed**, S. C. Kakon, et al., "An Integrated Evaluation Protocol for Adversarial Robustness, Generalization, and Explanation Stability in URL-Based Phishing Detection," *Frontiers in Computer Science*, vol. 8, Art. 1834407, 2026. [doi:10.3389/fcomp.2026.1834407](https://doi.org/10.3389/fcomp.2026.1834407)

Phishing URL classifiers report near-perfect accuracy on standard test sets. This project asks how they hold up when an attacker makes small, syntax-preserving changes to the URL, whether that robustness generalizes to unseen attack types, and whether the model's explanations stay stable under attack.

## What the protocol evaluates

| Axis | How |
|---|---|
| Clean accuracy | Domain-disjoint 70/15/15 split of PhiUSIIL (235k URLs, zero domain overlap), 95% bootstrap CIs |
| Adversarial robustness | 6 rule-based mutation families (homoglyph, typo, subdomain injection, padding, path injection, TLD swap) at 3 depths |
| Generalization to unseen attacks | Leave-one-family-out (LOFO) adversarial training |
| Combined attacks | All 15 pairwise family compositions, both orders |
| Explanation stability | SHAP top-20 feature overlap (Jaccard) and rank correlation, vs. a random-mutation baseline |
| External validity | PhishTank phishing URLs, plus a dual-source benign set |
| Defenses | Adversarial training, curriculum adversarial training, input randomization |

Models: TF-IDF + Logistic Regression, TF-IDF + XGBoost, character-level CNN, and fine-tuned BERT.

## Key results

| Model | Clean accuracy | Accuracy under attack (depth 2, mean over families) | Drop |
|---|---|---|---|
| Logistic Regression | 0.996 | 0.856 | 0.140 |
| XGBoost | 0.998 | 0.800 | 0.199 |
| Char-CNN | 0.998 | 0.709 | 0.289 |
| BERT | 0.999 | 0.681 | 0.318 |

- All four models exceed 99.6% clean accuracy, but **subdomain injection** reduces every model to about 43% accuracy, which is what you get by labeling every URL as phishing.
- Higher clean accuracy did not mean more robustness: BERT and Char-CNN degrade more than the linear models.
- Robustness to subdomain injection does **not** transfer when that family is held out of adversarial training (LOFO).
- Under subdomain attacks, the SHAP top-20 feature overlap falls to about 0.52, compared with 0.80 for random character edits, so explanations shift even when predictions look confident.
- Adversarial training recovers up to about 55 accuracy points on subdomain injection.

## Running

The notebook was developed on Google Colab with a GPU (BERT fine-tuning takes about 40 minutes).

1. Download the datasets:
   - **PhiUSIIL Phishing URL Dataset** (UCI Machine Learning Repository) -> `PhiUSIIL_Phishing_URL_Dataset.csv`
   - **PhishTank** verified online URLs -> `verified_online.csv`
2. Upload both to `/content/` in Colab (or edit the paths in the data-loading cell).
3. `pip install -r requirements.txt`
4. Run `phishing_robustness_evaluation.ipynb` top to bottom. All randomness is seeded (`SEED = 1337`). Result tables and figures are written to `/content/tables/` and `/content/plots/`.

Datasets and trained model weights are not included in this repository.

## Citation

```bibtex
@article{ahamed2026phishing,
  title   = {An Integrated Evaluation Protocol for Adversarial Robustness, Generalization, and Explanation Stability in URL-Based Phishing Detection},
  author  = {Ahamed, Tanvir and Kakon, Shawon Chakrabarty and others},
  journal = {Frontiers in Computer Science},
  volume  = {8},
  pages   = {1834407},
  year    = {2026},
  doi     = {10.3389/fcomp.2026.1834407}
}
```
