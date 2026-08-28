# Mental Health Text Classification — BiLSTM with Attention

A bidirectional LSTM with an additive attention mechanism, trained to classify
self-reported mental-health discourse from a public research corpus. Built as graduate
coursework in Artificial Intelligence and Machine Learning at George Washington
University.

**Status:** Academic coursework · **License:** BigScience OpenRAIL-M · **Not a clinical tool**

---

## ⚠️ Scope, Limitations, and Ethical Use

**Read this before anything else in this repository.**

This project exists to demonstrate sequence modeling and attention-based
interpretability. It was built for a graduate course. It has no clinical standing of any
kind.

### What this model is

A multi-class text classifier trained on self-reported, community-sourced posts from a
publicly available research dataset. Given a passage of text, it assigns a label drawn
from that dataset's taxonomy.

### What this model is not

- It is **not** a screening instrument and it performs no screening.
- It produces **no diagnosis**. Its outputs are dataset categories, not clinical
  determinations. No clinician produced, reviewed, or validated the labels it learned
  from, and none reviewed its outputs.
- It has **not** been clinically validated, evaluated against any diagnostic standard, or
  tested for safety in any applied setting.
- It is **not** deployed, and it is not offered as a product or a service.

### Label provenance

The training labels are self-reported and community-sourced — derived from where and how
people posted, not from assessment by a professional. Every claim about this model's
performance inherits that limitation. Reported metrics describe agreement with those
labels and nothing more. A model that agrees with the corpus is not a model that is right
about a person.

### Attention is not explanation

The attention heatmaps show which tokens the model weighted. They do not establish why a
prediction was made. Attention weights are a useful debugging surface and a poor
justification; they must not be presented to any reviewer, auditor, or affected person as
the reason for an outcome.

### Known risks

Models trained on social-media text encode the demographic, linguistic, and cultural skew
of the population that produced it. Performance almost certainly varies across groups this
corpus underrepresents, and in a mental-health context that variance lands on the people
least likely to be served well. **No fairness or subgroup evaluation has been conducted.**


### Do not use this to make decisions about people

Not for triage, risk assessment, hiring, admissions, benefits adjudication, insurance,
content moderation, or any other consequential determination about any real person. The
license attached to this repository prohibits several of these uses explicitly.

If you are considering an applied use, the work that has **not** been done here includes:
clinical validation against a diagnostic standard, fairness and subgroup evaluation, IRB
or equivalent ethical review, adversarial and robustness testing, and a documented
human-oversight process with a named accountable reviewer.

See [`MODEL_CARD.md`](MODEL_CARD.md) for the full governance treatment.

### If you need help

This repository is not a source of help. If you or someone you know is struggling, contact
the **988 Suicide & Crisis Lifeline** (call or text **988** in the US) or your local
emergency services.

---

## Dataset

| Property | Detail |
|---|---|
| Source | Sentiment Analysis for Mental Health (Kaggle) |
| File | `Combined Data.csv` |
| Input column | `statement` — free text from social media and forum posts |
| Target column | `status` — the corpus label taxonomy |
| Split | 70% train / 15% validation / 15% test, stratified |

The class distribution is imbalanced. This is why the *Results* section reports macro-F1
rather than overall accuracy.

---

## Architecture

```
Input text
    │
    ▼
Embedding layer  (300-d GloVe vectors, frozen in Phase 1)
    │
    ▼
2-layer bidirectional LSTM  (batch-first, dropout 0.5 between layers)
    │
    ▼
Attention  (linear → softmax → weighted sum over encoder outputs)
    │
    ▼
Dropout (0.5)
    │
    ▼
Fully connected → softmax → predicted class
```

### Hyperparameters

| Parameter | Value |
|---|---|
| Embedding dimension | 300 |
| LSTM hidden dimension | configurable (`HIDDEN_DIM`) |
| LSTM layers | 2, bidirectional |
| Dropout | 0.5 |
| Phase 1 optimizer | Adam, lr = 1e-3, embeddings frozen |
| Phase 2 optimizer | Adam, lr = 1e-4, weight decay = 1e-5, embeddings fine-tuned |
| Max gradient norm | configurable (`MAX_GRAD_NORM`) |
| Early stopping patience | 3 epochs |

**Two-phase training.** Phase 1 holds the GloVe embeddings frozen at a higher learning
rate. Phase 2 unfreezes and fine-tunes at a lower rate with weight decay. Early stopping
is applied in both phases.

---

## Results

| Metric | Phase 1 (frozen) | Phase 2 (fine-tuned) |
|---|---|---|
| Macro-F1 | — | — |
| Weighted-F1 | — | — |
| Accuracy | — | — |

### Per-class performance

| Class | Support | Precision | Recall | F1 |
|---|---|---|---|---|
| — | — | — | — | — |

## Setup

### Requirements

```
Python >= 3.9
torch >= 2.0
pandas
numpy
scikit-learn
matplotlib
seaborn
kagglehub
```

```bash
pip install torch pandas numpy scikit-learn matplotlib seaborn kagglehub
```

### Kaggle credentials

`kagglehub` requires a Kaggle API token at `~/.kaggle/kaggle.json`, or the
`KAGGLE_USERNAME` / `KAGGLE_KEY` environment variables.

### GloVe embeddings

The notebook downloads GloVe vectors automatically. If the download fails — network
restrictions in Colab are the usual cause — a random-initialization fallback is used
instead. **Training still runs, but results degrade materially. Any run using the fallback
should be labeled as such and not compared against a run with real embeddings.**

---

## Usage

### Google Colab

1. Open the notebook via the Colab badge.
2. Make Kaggle credentials available.
3. Runtime → Run all.

### Local

```bash
git clone https://github.com/JTunnessen/Mental-Health-Analysis-LSTM-RNN-with-Attention.git
cd Mental-Health-Analysis-LSTM-RNN-with-Attention
pip install torch pandas numpy scikit-learn matplotlib seaborn kagglehub
jupyter notebook v2_Mental_Health_Sentiment_Analysis.ipynb
```

Run cells top to bottom. Training takes several minutes on CPU; a GPU is recommended.

### Inference

```python
label, confidence = predict_sentiment("your text here")
```

Outputs are corpus labels with a softmax confidence. They are not clinical assessments and
must not be treated as such.

---

## License

Released under the **OpenRAIL-AMS** license: free access, modification, and
redistribution, subject to use-based restrictions that travel with the model and with any
derivative of it.

Those restrictions include prohibitions on using the model **to provide medical advice or
medical results interpretation**, for **fully automated decision-making that adversely
affects a person's legal rights**, and for uses that **discriminate against individuals on
the basis of legally protected characteristics**. The full list is in Attachment A of the
[`LICENSE.md`](LICENSE) file.

Two things to be clear about:

1. **OpenRAIL-M is not an OSI-approved open-source license.** Its behavioral restrictions
   are incompatible with clause 6 of the Open Source Definition, and GitHub will show the
   license as unrecognized. That is the intended trade-off — the restrictions are the
   reason for the choice.
2. **Earlier revisions of this README stated the MIT License**, though no `LICENSE` file
   was present in the repository. To the extent anyone relied on that statement, this
   change applies going forward and does not purport to revoke it.

---

## Citation

```bibtex
@misc{tunnessen2026mentalhealth,
  author = {Tunnessen, James E.},
  title  = {Mental Health Text Classification: A BiLSTM with Attention},
  year   = {2025},
  note   = {Graduate coursework, George Washington University
            School of Engineering and Applied Science},
  url    = {https://github.com/JTunnessen/Mental-Health-Analysis-LSTM-RNN-with-Attention}
}
```

