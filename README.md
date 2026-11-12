# ESG & Energy Return Prediction

**Python · Random Forest · Temporal validation · Portfolio analysis**

Do ESG scores help predict energy-company returns? This study examines 12 US and European companies over 2013–2024, modeling returns relative to sector benchmarks and comparing utility and oil & gas portfolios.

Random Forest regression supports the forecasting exercise; an annualized information ratio supports the portfolio comparison. The historical report and the revised Python evaluation are documented separately below.

## Reports

| Document | PDF | LaTeX source |
| --- | --- | --- |
| **Full report — Italian (original, 2025)** | [Read Italian report](report/report.pdf) | [Italian LaTeX](report/main.tex) |
| **Full report — English (translation)** | [Read English report](report/en/report.pdf) | [English LaTeX](report/en/main.tex) |
| Project overview — English (one page) | [Read overview](docs/project-page.pdf) | [Overview LaTeX](docs/project-page.tex) |

Both full reports describe the **historical 2025 analysis**. The updated Python evaluation uses a revised temporal holdout and has separate [results](docs/results.md) and [methodology notes](docs/methodology.md). Do not compare the two sets of estimates as if they came from the same validation procedure.

## Data and sample

| | United States | Europe |
|---|---|---|
| Utilities / transition | NextEra Energy, Xcel Energy, Duke Energy | Enel, Iberdrola, E.ON |
| Oil & gas | Exxon Mobil, Chevron, Occidental Petroleum | TotalEnergies, BP, ENI |

The input files cover monthly prices, sector-benchmark returns and E, S, G and overall ESG scores. I consider three periods: **2013–2016**, **2017–2020** and **2021–2024**.

The two groups are defined by business activity, not by an estimated cutoff in ESG scores. This matters when interpreting differences between them. [Data sources and file details](data/README.md).

## Results of the revised analysis

The PDF report preserves the methodology, formula, tables and conclusions of the original 2025 study. The results below instead refer to the later Python evaluation with calendar-month holdouts; the two sets of estimates should not be mixed.

- Random Forest test R² is **negative in five of the six** period–region combinations. The only positive result is for the US in 2017–2020 (**R² = 0.396**); that test covers just eight distinct months, so the finding needs further validation.
- In Europe in 2017–2020, the difference between the two portfolios' annualized information ratios is **1.676** (95% block-bootstrap interval **0.196 to 3.247**). The result is exploratory: the groups differ by industry, and the six comparisons have not been corrected for multiple testing.
- Across the other periods, the portfolio differences are less conclusive. The study does not establish a causal ESG premium.

## Method

Monthly stock returns are calculated from the available price series and expressed relative to a benchmark for each region. The prediction target is the **sum of the following 12 monthly relative returns**; E, S, G and overall ESG scores enter with a one-month lag.

Six Random Forest models (400 trees each) are fitted separately by period and region. Training and test sets are separated by **calendar month**, and training observations are removed when their forward-looking target reaches into the test period. Performance is compared against the mean target observed in training. The portfolio analysis uses equal-weight monthly returns and a paired six-month block bootstrap.

Details of the temporal split, the bootstrap procedure and differences from the earlier notebook are in [methodology notes](docs/methodology.md).

## Run the analysis

> **Data access:** The original source archive is now private. A fresh public clone cannot retrieve the inputs anonymously. Running the analysis requires authorized local copies of the three original files in `data/raw/`. The reports, methodology and recorded results remain available without those inputs.

Python 3.11 or later is recommended. After placing the original inputs in `data/raw/`, run from the repository root:

```bash
python -m pip install -r requirements.txt
python scripts/fetch_originals.py
python src/esg_analysis.py
```

The input script validates the recorded Git blob hashes of files already present locally. Its legacy download fallback still points to the private archive and cannot supply missing inputs to public users. The data are not committed here because redistribution permissions for the ESG panel have not been confirmed. See [data sources and file details](data/README.md).

Results are saved as CSV files under `outputs/`. To run the checks:

```bash
python -m unittest discover -s tests -p 'test_*.py' -v
```

An earlier analysis and test run is recorded in [GitHub Actions](https://github.com/Salvatore-Megna/esg-energy-return-modeling/actions/runs/41823752182). That run predates the archive becoming private; it does not establish that a fresh public clone can now retrieve the inputs. The retrieval script and data-dependent CI still need to be adapted to the restricted-data setup.

## Files

| Location | Contents |
|---|---|
| `src/esg_analysis.py` | Data preparation, models and portfolio analysis |
| `scripts/fetch_originals.py` | Original-input hash checks and legacy archive retrieval |
| `report/` | Original 2025 report, typeset in LaTeX without changing its content |
| `docs/results.md` | Full results by period and region |
| `docs/methodology.md` | Method choices and limitations |
| `tests/` | Unit and integration tests |

## Limitations

This is a small observational study. Overlapping 12-month targets, short test windows, sector differences and the treatment of dividends all limit the conclusions. The precise construction of the benchmark files and the dates on which ESG scores became available have not been independently established. The reported bootstrap intervals depend on the block length; they are not evidence of causality.
