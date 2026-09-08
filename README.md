<div align="center">

# 🧠 SmartML Studio

**An intelligent, end-to-end machine learning platform for structured tabular data**

Upload a dataset and SmartML Studio walks you through the whole workflow — profiling,
visualisation, preprocessing, feature engineering, model selection, training, ensembling
and explainability — recommending the next action at every step with a **confidence score**
and a **plain-English reason**. Nothing runs automatically; you always make the call.
</div>

---

## Table of Contents

- [Why SmartML Studio](#why-smartml-studio)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [The 11-Step Workflow](#the-11-step-workflow)
- [Getting Started](#getting-started)
- [Optional: AI-Written Explanations](#optional-ai-written-explanations)
- [Project Structure](#project-structure)
- [Testing](#testing)
- [Roadmap](#roadmap)

---

## Why SmartML Studio

Most AutoML tools are a black box: you hand over a CSV and get a model back with no
insight into *why* those choices were made. Notebooks are the opposite — total control,
but you rebuild the same boilerplate every single time.

SmartML Studio sits in between. Every module is an **advisor**, not an autopilot:

> *"I recommend **median imputation** for `age` — ⭐⭐⭐⭐ (87%). The column is 12% missing
> and right-skewed (skew = 1.8), so the mean would be pulled toward the tail."*

You see the recommendation, the confidence and the reasoning — then you decide.

---

## Key Features

| | Feature | What it does |
|---|---|---|
| 🎯 | **Confidence-scored advisors** | Every suggestion ships with a ⭐ rating, a percentage and a plain-English justification. |
| 📂 | **Universal dataset support** | Any structured tabular file (CSV / Excel) — classification or regression, auto-detected. |
| 🔬 | **Deep dataset profiling** | Type inference, missingness, skew, cardinality, outlier and class-imbalance checks. |
| 🛠️ | **From-scratch algorithms** | Vectorised **SVM** (hinge loss + gradient descent) and **kNN**, both written in pure NumPy alongside the library versions. |
| 🌲 | **Gradient boosting suite** | scikit-learn, XGBoost, LightGBM and CatBoost trained and compared side by side. |
| 🤝 | **Hybrid ensemble** | Combine any trained models via majority or weighted voting. |
| 💡 | **Explainable AI** | SHAP attributions for any individual prediction, plus global feature importance. |
| 📄 | **One-click report** | Export the trained model and a full pipeline report of every decision made. |

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 19 + TypeScript + Vite — single-page workspace, light/dark design system |
| **Backend** | FastAPI — REST plus a WebSocket for live training progress |
| **Language** | Python 3.10+ — modular and object-oriented |
| **ML libraries** | scikit-learn · XGBoost · LightGBM · CatBoost |
| **Custom algorithms** | SVM (hinge loss + GD) · kNN — implemented from scratch in NumPy |
| **Explainability** | SHAP |
| **Data** | pandas · NumPy · openpyxl |
| **Visualisation** | Plotly · Matplotlib · Seaborn |
| **Testing** | pytest |
| **Optional LLM layer** | Any OpenAI-compatible provider (Groq, OpenRouter, Together, Cerebras, …) |

---

## The 11-Step Workflow

```
 1. 📤  Dataset Upload            →  CSV / Excel ingestion and validation
 2. 🔍  Dataset Analysis          →  Profiling, quality report, target detection
 3. 📊  Visualisation Advisor     →  Recommends the right plot for each column pair
 4. 🔧  Preprocessing Advisor     →  Imputation, encoding, scaling, outlier strategy
 5. ⚙️  Feature Engineering       →  Interactions, binning, datetime and text features
 6. 🧠  Model Advisor             →  Ranks candidate models for *this* dataset
 7. 🏋️  Model Training            →  Trains the selected models, tracks every run
 8. 📈  Model Comparison          →  Metrics table, ROC / PR curves, confusion matrices
 9. 🎯  Prediction                →  Single model or hybrid ensemble inference
10. 🔬  Explainable AI            →  SHAP waterfall + global importance
11. 💾  Download & Report         →  Serialised model + full pipeline report
```

---

## Getting Started

### Prerequisites

- Python **3.10 or newer**
- `pip`, and a virtual environment (recommended)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/FenilKaneria/Personal-ML-Studio.git
cd Personal-ML-Studio

# 2. Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Install frontend dependencies
cd frontend && npm install && cd ..

# 5. Launch both servers
python run_dev.py
```

The app opens at **http://localhost:5173**, the API docs at
**http://127.0.0.1:8000/docs**. Upload any CSV and start at step 1.

To run them separately:

```bash
python -m uvicorn backend.main:app --reload --port 8000
cd frontend && npm run dev
```

---

## Optional: AI-Written Explanations

Several screens can add plain-English narration — why an advisor recommended a step, what
drives a model overall, why one prediction came out as it did, and an executive summary in
the report.

**This is entirely optional.** Without a key the app behaves exactly as it otherwise
would, using the standard static explanations.

```bash
cp secrets.toml.example secrets.toml
# then edit the file and add your provider API key
```

Any OpenAI-compatible provider works. Defaults live in `config/settings.py`; the
`LLM_BASE_URL` and `LLM_MODEL` settings override them.

### Guardrails

- The LLM **only rewrites numbers the pipeline already computed** as prose. It never
  selects a model, sets a confidence score, or changes a preprocessing decision — those
  stay with the rule-based advisors.
- **What is sent**: aggregate statistics, the model comparison table, and — only when you
  click *"Generate explanation"* — the SHAP attributions for that one prediction.
- **The uploaded dataset is never transmitted.** This is enforced by a test:
  `tests/test_llm_context.py`.

---

## Project Structure

```
smartml-studio/
├── run_dev.py                  # Starts the API and the web app together
├── backend/
│   ├── main.py                 # FastAPI application
│   ├── api/                    # Route modules, one per pipeline stage
│   └── core/                   # Session store, JSON serialisation, secrets
├── frontend/
│   └── src/
│       ├── api/                # Typed client
│       ├── store/              # Step order, locking, completion
│       ├── theme/              # Design tokens, light and dark
│       ├── components/         # Shell, primitives, charts
│       └── steps/              # One screen per pipeline module
├── config/
│   ├── settings.py             # Central configuration
│   └── logging_config.py       # Logging setup
├── src/
│   ├── profiling/              # Dataset profiler
│   ├── advisors/               # Recommendation engines (viz, preprocessing, model)
│   ├── preprocessing/          # Preprocessing pipeline
│   ├── features/               # Feature engineering
│   ├── models/                 # Model trainer + custom SVM / kNN
│   ├── ensemble/               # Hybrid voting ensemble
│   ├── explainability/         # SHAP explainer
│   ├── evaluation/             # Comparison metrics, feature importance
│   ├── reporting/              # Markdown report builder
│   └── llm/                    # Optional narration layer
└── tests/                      # pytest suite
```

---