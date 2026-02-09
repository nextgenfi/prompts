\# Synthetic Data Generation Prompts



This document contains the prompts used to generate synthetic data for the NextGenBank ML Predictive Engine.



---



\## Prompt 1: Initial Synthetic Data Generator



```

You are a senior Python data engineer helping generate synthetic data for an existing ML predictive engine.



I already have a real dataset with ~500 columns including:



pid

hhid

fname, lname, gender, age

readiness\_score

propensity\_score

propensity\_percentile

segment\_refined

routing\_bucket

many feature-engineered columns (src\_\*, ims\_\*, etc.)



Your task is to generate fully synthetic replacement data that mimics this structure and distributions.



Technical Requirements:

\- Python 3

\- pandas, numpy, faker, random

\- Single runnable script

\- Output CSV: synthetic\_scored\_contacts.csv



Core Columns:

\- pid

\- hhid

\- fname

\- lname

\- gender

\- age (21–80)

\- readiness\_score (0–1)

\- propensity\_score (0–1)

\- propensity\_percentile (0–100)

\- segment\_refined (cold/warm/hot/general\_population)

\- routing\_bucket (cold/warm/hot)



Feature Columns:

Create ~40–60 synthetic feature columns using prefixes:

\- src\_

\- ims\_

\- behavior\_

\- demo\_

\- finance\_



Numeric values only (0–10 or 0–1 floats).

Correlate higher feature values with higher readiness\_score and propensity\_score.



Behavioral Logic:

\- readiness\_score ↑ → propensity\_score ↑

\- percentile > 70 → hot

\- 40–70 → warm

\- <40 → cold

\- segment\_refined derived from scores.



Volume:

Generate only 100 synthetic rows.



Output:

\- Save CSV as synthetic\_scored\_contacts.csv

\- Print shape + head()



Script runs via:

python generate\_synthetic\_contacts.py



IMPORTANT:

\- Entirely synthetic

\- No real people

\- Preserve ML-friendly distributions



Return ONLY Python code in a single code block.

```



---



\## Generated Script: `generate\_synthetic\_contacts.py`



The script generates:

\- \*\*100 rows\*\* of synthetic customer data

\- \*\*53 columns\*\* including:

&nbsp; - Core identifiers (pid, hhid, fname, lname, gender, age)

&nbsp; - Scores (readiness\_score, propensity\_score, propensity\_percentile)

&nbsp; - Segments (segment\_refined, routing\_bucket)

&nbsp; - 42 feature columns with prefixes: src\_, ims\_, behavior\_, demo\_, finance\_



\### Key Features:

1\. \*\*Correlated Data\*\*: Higher readiness/propensity scores correlate with higher feature values

2\. \*\*Realistic Distributions\*\*: Uses beta distribution for score clustering

3\. \*\*Segment Logic\*\*:

&nbsp;  - Combined score >= 0.7 → hot

&nbsp;  - Combined score >= 0.4 → warm

&nbsp;  - Combined score >= 0.2 → cold

&nbsp;  - Otherwise → general\_population



\### Run Command:

```bash

cd ml

python generate\_synthetic\_contacts.py

```



---



\## Prompt 2: Database Import with Scoring



The import script (`import\_to\_db.py`) uses these scoring rules:



\### NBA Score Calculation:

```python

\# Base affinity from IMS scores (scaled 0-100) with random boost

affinity = min(100, (ims\_score / 8) \* 100 \* random\_boost)



\# NBA = weighted combination

base = affinity \* 0.35 + readiness \* 100 \* 0.25 + propensity \* 100 \* 0.25

random\_factor = np.random.uniform(20, 50) \* 0.15

nba\_score = min(100, base + random\_factor)

```



\### Age Adjustments:

\- Age >= 62: Reverse mortgage boost (1.4x), Refi slight reduction (0.9x)

\- Age 50-61: Reverse boost (1.1x), HELOC boost (1.15x)

\- Age < 35: New home boost (1.3x), Reverse penalty (0.2x)



\### Priority Thresholds:

\- Score >= 55 → \*\*hot\*\*

\- Score >= 40 → \*\*warm\*\*

\- Score >= 25 → \*\*cold\*\*

\- Score < 25 → \*\*low\*\*



\### Property Value Distribution (California-style):

\- 20% starter homes: $350K - $550K

\- 30% mid-range: $550K - $850K

\- 30% upper-mid: $850K - $1.2M

\- 20% luxury: $1.2M - $2.5M



---



\## Data Flow



```

scout-data.csv (434 real records)

&nbsp;       ↓

generate\_synthetic\_contacts.py

&nbsp;       ↓

synthetic\_scored\_contacts.csv (100 synthetic records)

&nbsp;       ↓

import\_to\_db.py

&nbsp;       ↓

PostgreSQL Database (534 total leads)

&nbsp;       ↓

NextGenBank Dashboard

```



---



\## Output Statistics (Example Run)



```

Total: 534 leads imported



Summary by routing bucket:

&nbsp; cold: ~170 leads

&nbsp; warm: ~280 leads

&nbsp; hot: ~84 leads



Summary by priority:

&nbsp; hot: ~80 leads (15%)

&nbsp; warm: ~190 leads (35%)

&nbsp; cold: ~190 leads (35%)

&nbsp; low: ~74 leads (14%)

```



---



\## Files Generated



| File | Description |

|------|-------------|

| `generate\_synthetic\_contacts.py` | Python script to generate synthetic CSV |

| `synthetic\_scored\_contacts.csv` | Output CSV with 100 synthetic records |

| `import\_to\_db.py` | Script to import both CSVs into PostgreSQL |

| `SYNTHETIC\_DATA\_PROMPTS.md` | This documentation file |





