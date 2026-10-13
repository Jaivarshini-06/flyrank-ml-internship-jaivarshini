# Capstone Report — <your lane>

- **Author:** Muthukrishnan Jaivarshini
- **Lane:** Refresh / Content Opportunity Scoring
- **Repo:** flyrank-ml-internship-jaivarshini
- **Date:** October 13, 2026

> Copy this file to `work/capstone_report.md` and fill it in as you build. Sections 1–8
> mirror the Pass / Needs-Work rubric axes, so nothing here is optional. Sections 0 and 9
> are **paper sections**: your deployed research paper must carry both, and they're here so
> you never rebuild them from memory at ship time.

## 0. Abstract

Five sentences, written last, placed first: question → data → method → headline result →
what the output is for. This is the top of your deployed paper.

## 1. Problem framing

The goal of this project is to help content editors prioritize which content items should be reviewed for potential search-performance declines.

The unit of analysis is a content-month record. The model uses historical performance metrics to estimate the likelihood of a decline of at least 20% in average daily impressions in the following month. Its output is a ranked review queue, with higher-risk records placed first.

A FlyRank editor can use this queue to investigate performance, check data availability, and decide whether a content update or further analysis is appropriate. A false positive may lead to unnecessary review work, while a false negative may cause a potentially declining item to be overlooked.

Machine learning is useful here because it combines multiple performance signals rather than relying only on a single low-impressions threshold. However, the ranking is a decision-support aid, not an automatic refresh decision, and it does not establish that refreshing content will improve performance.

## 2. Data safety

This project uses the FlyRank ML Internship warehouse dataset, release v20260703, a frozen snapshot exported on July 3, 2026. The analysis uses the `dim_content` table for content metadata inspection and monthly partitions of `fact_content_daily_performance` for historical performance analysis. The modeling dataset uses monthly average daily impressions, clicks, average search position, and the number of available reporting days.

The pseudonymous `client_hash_id` and `content_hash_id` fields are used to match records across months, not as predictive features. The model deliberately excludes `trend_direction` and `trend_pct` because these fields may contain information derived from performance trends and could introduce target leakage. Content URLs, client names, and private search queries are not included in the public-facing report.

The prediction target is whether average daily impressions decline by at least 20% in the following month. Only performance information from the current month is used as input to predict that target. Records without sufficient available reporting days are excluded from the monthly aggregates.

Before submission, the repository's `work/` directory must be checked to ensure that no client-identifying information, private URLs, private queries, credentials, or sensitive row-level data are included.

## 3. Baseline

The baseline is a low-impressions rule that flags a content item for review when its current month's average daily impressions are below the median current-month impressions in the training data. This provides a simple, transparent comparison against the machine-learning model and does not require fitting a predictive model.

The baseline and logistic regression model were evaluated on the same test period, using May 2026 performance to assess declines in June 2026. The baseline achieved 43.97% accuracy, 52.15% precision, 47.77% recall, and an F1-score of 0.4986.

The baseline offers a straightforward way to prioritize content based on low impressions, but it does not combine multiple performance signals. The model's results are compared with this rule to assess whether the additional complexity provides a useful improvement in identifying content at risk of declining impressions.

## 4. Model / analysis

The project uses logistic regression to estimate the likelihood that a content item's average daily impressions will decline by at least 20% in the following month. Logistic regression is suitable for this binary classification task because it provides a probability score that can be used to rank content items for review.

The model uses four features from the current month: average daily impressions, average daily clicks, average search position, and the number of days with available Google Search Console data. Missing feature values are handled using median imputation, and the features are standardized before fitting the model. Class weighting is used to account for differences in class frequencies.

The target is defined as a decline when the following month's average daily impressions are less than or equal to 80% of the current month's average daily impressions. The model is trained on March and April 2026 records and evaluated on May 2026 records, using June 2026 outcomes as the target. Pseudonymous client and content identifiers are used to match records across months but are not included as model features.

Content metadata, URLs, keywords, and label-derived trend fields are excluded from the predictive feature set. The resulting probability scores support prioritization for human review; they do not establish why a decline occurred or whether refreshing a page will improve its performance.

## 5. Evaluation

The evaluation uses a time-aware split to reflect the intended prediction setting. March and April 2026 records are used for training, while May 2026 records are held out for testing against June 2026 outcomes. This preserves chronological order, although the evaluation does not establish generalization to entirely unseen clients.

On the May-to-June test period, the logistic regression model achieved 60.93% accuracy, 64.14% precision, 74.89% recall, an F1-score of 0.6910, and a ROC-AUC of 0.6004. The positive-class base rate was 58.32%, meaning that approximately 58.32% of test records met the defined decline threshold.

On the same test period, the low-impressions baseline achieved 43.97% accuracy, 52.15% precision, 47.77% recall, and an F1-score of 0.4986. The model therefore performed better than this simple rule on the reported classification metrics. However, its ROC-AUC indicates only modest ability to distinguish between records that do and do not experience a decline.

The predicted probabilities for the two outcome groups overlap, so the model does not cleanly separate declining and non-declining records. These results are directional evidence on one held-out time period, not proof of performance across all clients or future periods. The evaluation also does not establish that any recommended content refresh will improve search performance.

## 6. Interpretation

The logistic regression model identified differences in the likelihood of a future impressions decline using current-month performance signals. Its probability scores provide a way to rank content records for review, but they should not be interpreted as certainty that a decline will occur.

The model achieved an F1-score of 0.6910, compared with 0.4986 for the low-impressions baseline. This suggests that combining multiple performance features was more useful on the test period than relying on the baseline rule alone. However, the ROC-AUC of 0.6004 indicates that the model's ability to distinguish between the two outcome groups remains modest.

The predicted probability distributions for records with and without a decline overlap substantially. Therefore, the model cannot reliably separate every record into clearly distinct risk groups. The results support using the scores as one signal for prioritization rather than as a definitive judgment about content quality or future performance.

A key limitation is that the model uses only four current-month performance features and has been evaluated on a single held-out month. Further testing across additional time periods and clients would be needed to determine how stable and useful the rankings are in practice.

## 7. Recommendation

The project produces a ranked review queue based on each content record's predicted probability of an impressions decline of at least 20% in the following month. Records with higher scores are prioritized for review, while lower-scoring records can be monitored rather than immediately escalated.

A FlyRank editor could use the queue to check whether the performance data is complete, investigate recent changes, review the content's relevance and quality, and decide whether an update or further analysis is warranted. The score should guide investigation rather than trigger an automatic refresh.

Confidence in the approach is moderate for identifying records worth investigating within the evaluated period, but limited for broader use. The model's ROC-AUC of 0.6004 indicates modest discrimination, and the evaluation covers only one held-out month. Additional time periods and client-level validation would be needed before relying on the rankings operationally.

The analysis is predictive, not causal. It does not show that refreshing content will increase impressions, nor does it predict Google's ranking algorithm. Any refresh decision should be made by a human editor using context beyond the model score.

## 8. Reproducibility

The exact commands to re-run everything from a fresh clone, your random seeds, and your
environment (`pip freeze` highlights or `requirements.txt` deltas). If you claim a sealed or
holdout evaluation, two things must be committed: the cell/script that builds the sealed
frame, and the metrics file it produced — "evaluated once, blind" should be checkable from
your repo, not taken on faith.

## 9. Acknowledgments & data credit

One short section at the bottom of the deployed paper: "Built on the FlyRank ML Internship
dataset" **linking to https://flyrank.ai**. Crediting your data source is standard research
practice — and it's on the capstone's required-section list, so a paper without it isn't done.

---

> **Claims checklist before submitting:** observed / measured / directional / decision-support
> **Metrics vs. base rate:** report your task's base rate (majority-class %) next to any
> precision@K or accuracy — a high score can just be a high base rate. AUC / lift over
> baseline are the honest discrimination numbers.
> language everywhere · no causal claims without an experiment or causal design · no
> "predicted Google's algorithm" · no client-identifying details · numbers in this report
> match a fresh re-run.
