# Phishing URL & Content Detector

Streamlit app combining two Random Forest models — one on URL structure (40 features), one on page content (43 features).

## Files in this repo

| File | Purpose |
|---|---|
| `app.py` | Main Streamlit app — entry point |
| `url_feature_extractor.py` | 40 URL-structure features (no network call) |
| `content_feature_extractor.py` | 43 page-content features + SSRF-safe fetcher |
| `signals_panel.py` | Renders the "what we found on the page" panel |
| `requirements.txt` | Python dependencies |
| `url_random_forest_model.pkl` | **You must add this** — trained URL model |
| `content_rf_model.pkl` | **You must add this** — trained content model |

## Before you push to GitHub

`app.py` loads both `.pkl` model files from the same folder it runs in
(`Path(__file__).parent`). They are **not** included here — copy your trained
model files into this folder before committing, e.g.:

```bash
cp /path/to/url_random_forest_model.pkl .
cp /path/to/content_rf_model.pkl .
```

If either file is over ~50–100 MB, regular `git push` may reject it — use
[Git LFS](https://git-lfs.com/) instead (`git lfs track "*.pkl"`).

Also worth a quick local check before deploying: after loading a model, print
`model.classes_`. `phishing_probability()` in `app.py` looks up the class
labeled `1` — if your models were trained with string labels (e.g.
`"phishing"/"legitimate"`) instead of `0/1`, every verdict card will silently
show "Not available." Re-map `phishing_probability()` if so.

## Push to GitHub

```bash
cd deploy
git init
git add .
git commit -m "Phishing detector Streamlit app"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

## Deploy on Streamlit Community Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io) and sign in with GitHub.
2. **New app** → pick your repo, branch `main`, main file path `app.py`.
3. Deploy. First build installs everything in `requirements.txt`.

## Note on `feature_extractor.py`

The uploaded `feature_extractor.py` was left out of this folder. It's an
earlier draft of `url_feature_extractor.py` — not imported anywhere in
`app.py` — and its `safe_fetch()` calls `requests.get()` without importing
`requests`, so it would throw `NameError` if ever called. Safe to leave out
of the repo; keep it only if you want it for reference elsewhere.
