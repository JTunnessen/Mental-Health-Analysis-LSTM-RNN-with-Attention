# Model Card — Mental Health Text Classification (BiLSTM + Attention)

**Version:** 1.0 · **Date:** 2026 · **License:** BigScience OpenRAIL-M
**Author:** James E. Tunnessen Jr.
**Context:** Graduate coursework, Artificial Intelligence & Machine Learning, George Washington University School of Engineering and Applied Science

> Model cards follow Mitchell et al., *Model Cards for Model Reporting* (FAT\* 2019).
> This card is the governance artifact for the model; the README is the engineering
> documentation. Where they disagree, this card governs.

---

## 1. Model details

| Field | Value |
|---|---|
| Task | Multi-class text classification |
| Architecture | 2-layer bidirectional LSTM with additive attention |
| Embeddings | GloVe 300d, pre-trained; frozen then fine-tuned |
| Framework | PyTorch 2.0+ |
| Output | Softmax distribution over the source corpus label taxonomy |
| Training regime | Two-phase (frozen → fine-tuned), Adam, early stopping patience 3 |
| Status | Academic coursework. Not deployed. Not maintained as a product. |

---

## 2. Intended use

**Intended.** Demonstration of sequence modeling and attention-based interpretability on
a public research corpus. Educational reference for students and practitioners. A worked
example for discussions of governance requirements around sensitive-category inference.

**Out of scope — explicitly.** Any use that produces, informs, or influences a
determination about a real person. This includes clinical screening, diagnosis, triage,
risk scoring, employment or admissions decisions, benefits adjudication, insurance
underwriting, law enforcement, and content moderation with consequences for the author.

**Out of scope — technical.** Text materially unlike the training corpus: clinical notes,
transcribed speech, non-English text, text from populations not represented in the source
data, or any domain where the label taxonomy does not apply.

---

## 3. Factors

**Groups.** The corpus is drawn from public social media and forum posts. It carries the
demographic, linguistic, and cultural distribution of people who post publicly about
mental health on those platforms — skewed by age, geography, language variety, platform
norms, and willingness to self-disclose.

**Instrumentation.** Labels derive from posting context, not from clinical assessment.
The model learns to predict *where a post came from and how it was tagged*, which is a
proxy for the construct of interest and not the construct itself.

**Environment.** Performance is undefined for any input distribution other than the one
described. No domain-shift evaluation has been performed.

---

## 4. Metrics

**Primary metric: macro-F1.** Chosen because the class distribution is imbalanced;
overall accuracy is inflated by the majority class and would misrepresent performance on
the classes that matter most.

**Also reported:** per-class precision, recall, and F1 with support counts; confusion
matrix; weighted-F1.

**Decision threshold.** Not tuned. No operating point has been selected, because
selecting one would imply an application, and there is none.

> *Populate the results table in the README, then reference it here. If results are
> withheld pending fairness evaluation, say so in this section explicitly.*

---

## 5. Evaluation data

Held-out stratified 15% split of the same corpus. **No external validation set. No
clinical benchmark. No temporal holdout.** Evaluation therefore measures internal
consistency with the source labels and provides no evidence of real-world validity.

---

## 6. Training data

"Sentiment Analysis for Mental Health" (Kaggle), `Combined Data.csv`. Public research
dataset aggregating self-reported mental-health discourse. Stratified 70/15/15 split.

**Known data limitations:** self-reported and community-sourced labels; aggregation from
multiple sources with differing conventions; possible duplication across source
communities; no consent framework covering secondary ML use; no demographic metadata,
which is precisely why the fairness evaluation below cannot currently be performed.

---

## 7. Ethical considerations

**Sensitive category.** Mental-health status is a special category of personal data under
GDPR Article 9 and is treated as sensitive under most comparable regimes. Inference of a
sensitive category from behavioral text is a materially higher-risk operation than the
same architecture applied to product reviews.

**Consequence asymmetry.** A false negative and a false positive are not symmetric here.
In any applied setting, one risks missed harm and the other risks stigma, surveillance,
or unwarranted intervention. No cost-sensitive analysis has been performed.

**Attention and explanation.** Attention weights indicate where the model attended. They
are not a causal account of the decision and must not be offered as a justification to an
affected person or a reviewer.

**Consent.** Authors of the source posts did not consent to their text training a
classifier. The corpus is public and permissively licensed for research; that is a legal
basis, not an ethical resolution.

---

## 8. Caveats and recommendations

**This model should not be deployed.** If a comparable system were being considered for
an applied setting, the following would be prerequisites, not follow-ups:

1. **Clinical validation** against a recognized diagnostic standard, with clinician
   involvement in design and review.
2. **Fairness evaluation** across demographic subgroups, which requires demographic
   metadata this corpus does not carry — meaning a different dataset.
3. **IRB or equivalent ethical review.**
4. **A documented human-oversight process:** a named accountable reviewer, a defined
   intervention point before any output reaches a decision, and authority to override.
5. **Post-decision reconstruction:** the ability to reproduce and explain a single
   determination for a named individual months later, on request.
6. **Scope control:** a written authorization boundary and a scheduled review, because the
   distance between "flags signals in research text" and "screens real people" is one
   procurement cycle.
7. **Adversarial and robustness testing**, including behavior under paraphrase, code-
   switching, sarcasm, and deliberate evasion.

Items 4 through 6 map to NIST AI RMF **GOVERN** and **MANAGE** functions and are the ones
most often skipped, because they are organizational rather than technical.

---

## 9. Contact

Issues and corrections: via this repository. This model is unmaintained coursework and
carries no support commitment.
