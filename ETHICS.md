# Ethical Statement

This document describes how data is collected in this project, the risks involved, how those risks are reduced, and the limits of the work.

## Principles

- **Open, public data only.** Both sources (Open-Meteo and OpenWeather) publish weather data intended for public use.
- **No personally identifiable information (PII).** Requests contain only a city name or a pair of coordinates for a public area. Responses contain only weather values. Nothing about any individual is collected.
- **Terms of Service compliance.** Open-Meteo's free API is used for non-commercial, educational work as its terms allow, and its data is attributed under CC BY 4.0. OpenWeather is used on a free account within its rate limits and is credited in the README.
- **Responsible API use.** The notebook makes one request per source per run, sets a 10-second timeout, and retries Open-Meteo at most three times with exponential backoff (1s, 2s, 4s), well below either provider's free-tier limits.
- **Secrets stay out of the repo.** The OpenWeather API key is entered at runtime with `getpass` and is never hard-coded. `.env` files are excluded by `.gitignore`.
- **Full provenance.** Every saved dataset is logged in `DATA_README.md` with its UTC timestamp, endpoint, parameters, output file name, SHA-256 hash, and software versions.
- **Academic integrity.** The workflow follows Dakota State University's academic integrity and reproducibility expectations.

## Potential Risks

| Area | Risk |
|---|---|
| **Privacy** | Very low. The coordinates and city name point to public areas, not people. A location tied to a student's account could loosely hint at where they live or study. |
| **Security** | The API key could leak if it were typed into a code cell, saved in notebook output, or committed in a `.env` file. The notebook prints the first 6 characters of the key as a check, which ends up in the saved output if outputs are not cleared. |
| **Bias / representativeness** | Data comes from one location and one day of forecasts. Open-Meteo values are model forecasts, not ground-station observations. The two sources also query slightly different places (Sioux Falls vs. 44.6°N, 96.8°W). Results should not be generalized to a region or treated as measured conditions. |
| **Licensing / ToS** | Using the data commercially, or redistributing it without attribution, would break the providers' terms. |
| **Data integrity** | A saved CSV could be changed after the fact with no way to notice. |

## Mitigations

- **Key handling:** Key is entered with `getpass`; `.env` is git-ignored. Clear all notebook outputs before committing so the key prefix is not saved.
- **Integrity:** Each CSV is hashed with SHA-256 at save time, and the hash is logged so any later change can be detected.
- **Bias:** The README and this file state the single-location, forecast-only scope so readers do not over-interpret the data. Any analysis should compare against observed data before drawing conclusions.
- **Compliance:** Attribution for both sources is in the README. Use stays educational and non-commercial.
- **Rate limits:** Single requests per run with timeouts and capped backoff keep load on the providers minimal.

## Limitations

- **Not bit-for-bit reproducible.** Forecasts change over time, so a re-run returns different values. The log makes the *method* reproducible and lets a saved file be verified, but it cannot recreate past API responses.
- **Data is not in the repo.** `data/` is git-ignored, so the logged hashes cannot be checked without the original CSV files.
- **OpenWeather results are not logged.** That cell only displays its result; it does not save a file or write a provenance entry.
- **Colab-specific paths.** The notebook assumes the repo is cloned to `/content/csc786-ethics-gunnar` and will need path changes to run elsewhere.
- **Versions are recorded, not pinned.** Python, pandas, and requests versions are logged per run, but there is no `requirements.txt` to install the same versions.
- **Small scope.** One location and one variable (hourly 2 m temperature). This is a workflow demonstration, not a dataset meant for analysis.
