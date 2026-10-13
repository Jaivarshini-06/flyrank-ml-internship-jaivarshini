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

Which data you used and which columns you deliberately excluded (and why). Leakage risks you
considered — especially label-derived fields (`trend_direction`, `trend_pct`) and pseudonymous
IDs (grouping only, never features). Confirm nothing client-identifying appears anywhere in
`work/`.

## 3. Baseline

The transparent rule or score you built first. Why it's a fair comparison, and its numbers on
the same data and metric as your model.

## 4. Model / analysis

Your method and why it fits the lane. The exact feature list (and what you left out on
purpose). The target or proxy definition, in one sentence.

## 5. Evaluation

Your split (grouped by client? time-aware?) and why. Metrics, model vs baseline **on the same
split**. What the errors look like — a short error analysis beats a big metric table.

## 6. Interpretation

What the model/clusters actually found. Feature importances or cluster profiles in plain
words. Surprises and negative results — a well-understood "no effect" is a valid result.

## 7. Recommendation

The ranked actions or decisions your output supports, and how a FlyRank editor would use them
tomorrow. State your confidence and the limits explicitly.

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
