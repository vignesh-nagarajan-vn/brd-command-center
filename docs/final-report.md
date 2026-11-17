# Final report

BRD Command Center is a local Streamlit app for a feedlot manager. The shortage path runs on synthetic data.

## What the slice does

Four yards, with Lot 4401 ranked first and explained by its drivers. Current practice is a named rule: veterinary review only when arrival weight is under 600 lb and there are at least two sources. Every alternative is compared with that rule. Staff hours, pen space, and treatment throughput are hard PuLP limits, and welfare floors stay on.

The demo cut takes TX-A from 220 staff hours to 90. Lot 4401 moves from enhanced surveillance to standard care. Expected cases go from about 103.5 to 109.4, BRD deaths from 17.1 to 18.1, operating loss from about $15,954 to $16,545, and antimicrobial exposure rises with the cases. Lot 4388 is missing travel, weaning, and vaccination. The app withholds a confident call and plans it on the worst case of its unknown-state range (18.6%), not the 5.6% that leaving the fields out would give. The text export is labeled synthetic. A CSV upload rejects bad rows and a file that is missing `head_count`. Accepted rows can join the plan or replace the seeded cohorts, and every yard's staff, pen, and treatment limit has a live slider. An infeasible solve names the yard limit that is short.

After that path, a morbidity-cost control ($151.18 versus a $450 stress value) can flip Lot 4401 to veterinary review, and four objectives share the same optimizer. The risk score is calibrated to Rojas et al. 2022: a 1,733-cohort synthetic population matches its 6.30% mean, 10.85% high-risk share, and AUC midpoint. The unfitted group means (25.3% and 3.99%) sit close to Rojas's 27.1% and 3.77%. Neither extra is on the three-minute path.

The technical brief is `docs/technical_brief.pdf`: three body pages plus a references page, built from the Markdown by `docs/build_brief.py`.

## Checks

`pytest`: 44 passed. That includes risk math, separate morbidity and metaphylaxis dollars, constraint feasibility, the staff cut, the cost flip, CSV upload dry-run accept/reject, the storyboard in the app, and ASGI `/` `/summary` `/health` `/ready` coverage.

Launch, from the repository root:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
streamlit run streamlit_app.py
```

No paid key. The demo CSVs are already in `data/`, next to `data/generate_demo_data.py`. The data dictionary is `docs/data_dictionary.md`.

## Repository

https://github.com/snr2753423/brd-command-center, branch `main`.

## Limits

The 15% benchmark, the $151.18 and $23.60 constants, and the 50% metaphylaxis efficacy are published (Rojas et al. 2022). The other effect sizes are assumptions, not validated accuracy. Medication actions require veterinary review. The weather-stress flag is synthetic. There is no live weather API.
