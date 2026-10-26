# Reproducibility Demo – CSC 786

**Author:** Gunnar Andrews  
**Course:** CSC 786, Module 5 Activity 1 – Dakota State University

This repository demonstrates an ethical, reproducible data-collection workflow. A Google Colab notebook pulls weather data from two public APIs, saves the results with a UTC timestamp, hashes each output file, and appends a provenance record to `DATA_README.md` so every collection run can be traced and verified.

## Data Sources

| Source | Endpoint | Parameters | Key required? | Saved & logged? |
|---|---|---|---|---|
| [Open-Meteo](https://open-meteo.com/) | `https://api.open-meteo.com/v1/forecast` | `latitude=44.6`, `longitude=-96.8`, `hourly=temperature_2m`, `forecast_days=1` | No | Yes – CSV in `data/`, entry in `DATA_README.md` |
| [OpenWeather](https://openweathermap.org/) | `https://api.openweathermap.org/data/2.5/weather` | `q=Sioux Falls`, `units=metric` | Yes (free account) | No – displayed in the notebook only |

Only public, aggregate weather data is collected. No personal data is requested or stored.

## Repository Contents

| File | Purpose |
|---|---|
| `Gunnar_Andrews_CSC786_Ethics_.ipynb` | Colab notebook that collects, saves, hashes, and logs the data |
| `README.md` | Project overview and usage instructions |
| `ETHICS.md` | Ethical statement: risks, mitigations, and limitations |
| `DATA_README.md` | Append-only provenance log, one JSON entry per collection run |
| `LICENSE` | License for the code and documentation in this repo |
| `.gitignore` | Keeps `data/`, `.env` files, and notebook checkpoints out of version control |

## How to Run

1. Open the notebook in Google Colab.
2. Clone the repo into the path the notebook expects:
   ```python
   !git clone https://github.com/g0ldencybersec/csc786-ethics-gunnar.git /content/csc786-ethics-gunnar
   %cd /content/csc786-ethics-gunnar
   ```
3. Run the cells in order. When prompted, paste your OpenWeather API key ([free sign-up](https://home.openweathermap.org/users/sign_up)). The key is entered with `getpass`, so it is never written to the notebook or the repo.
4. The Open-Meteo cell retries up to three times with exponential backoff if a request fails.
5. The final cells save `data/hourly_temps_<UTC timestamp>.csv`, compute its SHA-256 hash, and append a record to `DATA_README.md`.
6. (Optional) Uncomment the last cell to commit and push the updated log.

**Dependencies:** Python 3, `pandas`, `requests`, `python-dotenv`. The exact Python, pandas, and requests versions used are recorded in each `DATA_README.md` entry.

## Provenance Log Format

Each run appends one line to `DATA_README.md`:

```json
{"timestamp_utc": "2026-10-26T214500Z", "endpoint": "https://api.open-meteo.com/v1/forecast", "params": {"latitude": 44.6, "longitude": -96.8, "hourly": "temperature_2m", "forecast_days": 1}, "output": "hourly_temps_2026-10-26T214500Z.csv", "sha256": "<64-char hash>", "python": "3.x.x", "pandas": "x.x.x", "requests": "x.x.x"}
```

To verify a saved CSV, recompute its hash and compare it with the logged value:

```bash
sha256sum data/hourly_temps_<timestamp>.csv
```

## Reproducibility Notes

- The CSV files in `data/` are excluded by `.gitignore`, so the repo holds the **record** of each run (parameters, timestamp, hash, versions), not the data itself.
- Open-Meteo returns a live forecast, so re-running the notebook produces new values. Reproducibility here means the *method* and *parameters* are fully documented and any saved file can be checked against its hash.
- See `ETHICS.md` for known limitations.

## Data Attribution

- Weather data by [Open-Meteo.com](https://open-meteo.com/), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Used under Open-Meteo's free, non-commercial terms.
- Weather data provided by [OpenWeather](https://openweathermap.org/).

## License

Code and documentation in this repository are released under the MIT License – see [`LICENSE`](LICENSE). Data retrieved from the APIs above remains under each provider's own license and terms.
