# BRD Command Center

A decision aid for feedlot managers handling bovine respiratory disease (BRD) in the first 45 days after arrival. It scores each arrival cohort's BRD risk and shows how uncertain that score is. It compares treatment options against the yard's current practice on cases, deaths, cost, and antimicrobial use. It then builds a plan that fits each yard's staff, pen, and treatment limits. Medication decisions stay with a veterinarian.

- **Competition:** AISFS × Merck Animal Health Interdisciplinary Hackathon 2026, hosted by the AISFS Club (Artificial Intelligence for Sustainable Livestock Systems), Texas A&M University
- **Track:** Problem 1, BRD Decision Aid
- **Team:** Srivanth Rudrangi, Vignesh Nagarajan, Krish Hariharan
- **Tech stack:** Python 3.11+, Streamlit, pandas, NumPy, PuLP (CBC solver), Plotly, pytest
- **Data:** synthetic cohorts and yards, calibrated to Rojas et al. 2022 (1,733 feedlot cohorts). No paid APIs, accounts, or database.
- **Submission:** this repository, with the [technical brief](docs/technical_brief.pdf), the [demonstration case](docs/demonstration_case.md), and the [AI disclosure](docs/ai_disclosure.md)
- **Demo video:** [docs/demo_video.mp4](docs/demo_video.mp4) (4:52, narrated walkthrough of the Shortage scenario)
- **Contributing:** see [CONTRIBUTING.md](CONTRIBUTING.md) for setup, tests, Streamlit vs ASGI, PR/CI expectations, and synthetic-data notes
## Deployment

Requires Python 3.11+. Demo data ships in `data/`, and no secrets or `.env` file are needed.

```bash
git clone https://github.com/vignesh-nagarajan-vn/brd-command-center
cd brd-command-center
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pytest
streamlit run streamlit_app.py
```

The app opens on the Shortage scenario. On a Unix shell, `make install`, `make test`, and `make run` do the same steps.

To serve the shortage page without Streamlit, run `uvicorn app:app`. It exposes `/`, `/summary`, `/health`, and `/ready`. For Streamlit Community Cloud, point it at `streamlit_app.py`. Details are in the [hosting notes](docs/hosting.md).

To rebuild the demo data and brief, run `python data/generate_demo_data.py`, `python data/calibrate_to_rojas.py`, and `python docs/build_brief.py` (needs `pip install markdown`).

## Repo Roadmap

```text
streamlit_app.py        Dashboard: Overview, Cohort, Compare, Optimizer, Export
app.py                  ASGI entry (uvicorn app:app): /, /summary, /health, /ready
src/
  risk_model.py         45-day morbidity score, drivers, unknown-field planning
  calibration.py        Fit to Rojas 2022 mean, high-risk share, and AUC
  economics.py          Cases, deaths, operating loss, antimicrobial exposure per action
  optimizer.py          PuLP plan under staff, pen, treatment limits and welfare floors
  practice.py           Named current-practice rule (the baseline)
  validation.py         CSV upload checks
  assumptions.py        Every constant, tagged published, derived, or assumption
data/
  generate_demo_data.py Writes the demo cohorts and yard capacities
  calibrate_to_rojas.py Refits the score and writes calibration_*.csv
  upload_*.csv          Dry-run CSV fixtures (valid / bad rows / missing column)
docs/                   Brief, demo case, and supporting notes (see below)
tests/                  pytest suite (risk, costs, optimizer, calibration, app, ASGI)
```

### Docs

- [Technical brief](docs/technical_brief.pdf): three pages plus references
- [Demonstration case](docs/demonstration_case.md): one end-to-end decision for Lot 4401
- [Evidence registry](docs/evidence_registry.md): source for every number
- [Data dictionary](docs/data_dictionary.md): columns, units, and unknowns
- [SME briefing](docs/sme_briefing.md): assumptions and the current-practice rule
- [AI disclosure](docs/ai_disclosure.md): AI tools and external resources
