# LLM Pairwise Agreement Dashboard Guide

This guide describes the evaluation dataset, the agreement metrics shown in the dashboard, and how to use its filters and views.

## Dashboard and dataset

Open [Evaluation-spe.html](./Evaluation-spe.html) in a modern browser. The dashboard starts with a small built-in example dataset; it does **not** load [llm_evaluation.csv](./llm_evaluation.csv) automatically. Use **Load Dataset** to upload the CSV when you want to see those evaluations.

The CSV uses one row per model rating, with these exact column names:

| Column | Meaning | Example |
| --- | --- | --- |
| `JSON name` | Identifier for the evaluated item | `json1` |
| `Dimension` | Evaluation criterion | `Structure & consistency` |
| `LLM` | Name of the model/rater | `Gemini` |
| `Assessment` | Categorical rating | `Correct` |

The dashboard compares the three expected raters: `Gemini`, `Claude`, and `ChatGPT`. Each item is identified by its `JSON name` **and** `Dimension`; it should have exactly one rating from each of those models. The current CSV has 150 rating rows, representing 50 item-and-dimension combinations across five dimensions (10 combinations per dimension). Its assessment values are `Correct`, `Incorrect`, `Unclear`, and `N/A`.

Use a CSV with a header row and the four column names above. The CSV reader expects simple comma-separated rows; avoid commas or line breaks inside quoted field values. Rows missing one or more of the three model ratings are omitted from the agreement calculations. Model names and assessment labels are case-sensitive.

## Evaluation metrics

The dashboard reports agreement, not model quality against an independent ground truth. Each model is treated as a rater, and the statistics describe how consistently the raters assign labels.

### Pairwise agreement

For each of the three model pairs, the dashboard calculates:

- **Observed agreement**: the proportion of items for which the pair assigned the same label.
- **Expected agreement**: the agreement expected by chance, calculated from each model's rating frequencies.
- **Cohen's kappa (κ)**: chance-adjusted agreement, calculated as `(observed agreement - expected agreement) / (1 - expected agreement)`. A value of 1 means perfect agreement; 0 means agreement at the estimated chance level; a negative value means less agreement than expected by chance.

The top-card **Overall Pairwise Agreement** is the unweighted average of observed agreement for the three pairs. **Highest Pair Correlation** identifies the pair with the highest kappa; “correlation” here refers to this kappa comparison, not a correlation coefficient.

### Fleiss' multi-rater kappa

**Fleiss' kappa** summarizes chance-adjusted agreement across Gemini, Claude, and ChatGPT together. It complements the pairwise results: pairwise scores can show which two raters align, while Fleiss' kappa summarizes all three as a group.

The dashboard labels kappa values using these thresholds: above 0.80 is Almost Perfect; above 0.60 is Substantial; above 0.40 is Moderate; above 0.20 is Fair; above 0 is Slight; and 0 or less is Poor. These labels are interpretive guides, not significance tests. Kappa is sensitive to rating prevalence and sample size, so review the sample count and category distribution alongside the score.

### Important `N/A` limitation

The dashboard's statistical categories are currently `Correct`, `Incorrect`, and `Unclear`, but the supplied CSV also contains `N/A`. Complete rows containing `N/A` are retained in the sample count and can count as matching in observed pairwise agreement, while `N/A` is not included in the chance-agreement category frequencies. It is also omitted from the displayed confusion matrix and rating-frequency chart. Fleiss' kappa likewise uses three configured categories while assuming three ratings per item. As a result, current kappa and category visualizations may be misleading when `N/A` values are present.

For reliable comparisons, either replace `N/A` with a valid category according to a documented labeling policy, or update the dashboard to handle `N/A` consistently (for example, exclude any item with an `N/A` before calculating all metrics). Do not interpret scores from a dataset containing `N/A` as directly comparable with scores from a dataset that does not.

## Dashboard views and controls

1. **Load Dataset** — Select a CSV file with the expected schema. The dashboard pivots its rows into one record per `JSON name` and `Dimension`, then recalculates the views. The badge shows the active filename.
2. **Select Dimension** — Choose a dimension or **All Dimensions (Combined)**. This filters the top metrics, pairwise summary and cards, agreement chart, confusion matrix, and rating distribution. The dimension breakdown continues to show all dimensions.
3. **Select Rater Pair** — Choose the two raters shown in the confusion matrix. The pairwise summary and agreement chart continue to show all three pairs.
4. **Pairwise Matrix & Overview** — Shows the pairwise observed agreement, expected agreement, kappa, interpretation, and a comparison chart. Cards below provide a compact summary for each pair.
5. **Dimension Breakdown** — Compares each model pair's kappa across dimensions in a radar chart and metrics table. The item count is the number of complete item-and-dimension records for that dimension.
6. **Confusion Matrix** — Shows the selected pair's cross-tabulation: rows are the first rater, columns the second. The adjacent chart compares rating frequencies for all three models.
7. **Data Explorer** — Shows the pivoted, complete three-rater records and whether the three ratings have full consensus, a partial match, or disagreement. Search matches JSON names and dimensions.
8. **Reset Default Dummy Dataset** — Replaces the uploaded dataset with the dashboard's built-in example data.

The dashboard uses browser-loaded libraries from public CDNs, so an internet connection may be needed for styling and charts to load. The uploaded CSV is read locally in the browser.
