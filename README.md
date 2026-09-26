# WhatsApp Digital Forensics Analysis Toolkit

A Python-based digital forensics and natural language processing (NLP) framework designed to ingest, parse, and analyze exported WhatsApp chat transcripts (`.txt`). The toolkit identifies conversational trends, quantifies sender activity, and uses fuzzy string matching to detect illicit, high-risk, or policy-violating language across distinct threat vectors.

---

## Overview

In digital forensics and incident response (DFIR) investigations involving mobile communications, manual review of exported chat histories is time-consuming and prone to human oversight. This toolkit automates:

1. **Structured Log Extraction:** Parsing raw, unformatted text into structured, time-indexed pandas DataFrames.
2. **Behavioral Volume Analytics:** Tracking chronological message frequency, peak activity periods, and communication velocity.
3. **Actor Profiling:** Quantifying participation per sender to establish key communicators.
4. **Keyword & Context Flagging:** Employing fuzzy string matching to counter intentional typos, leetspeak, and obfuscated terminology across critical forensic categories.
5. **Attribution Reporting:** Correlating flagged keywords to specific senders to identify malicious actors or vulnerable participants.

---

## Key Features

* **Deterministic Text Ingestion:** Converts raw multi-line WhatsApp exports into structured time-series data (`timestamp`, `sender`, `message`).
* **Timeline Analysis:** Identifies communication surges (highest/lowest activity dates) and visualizes communication patterns over time.
* **Fuzzy Threat Identification:** Uses Levenshtein-based partial ratio matching (`fuzzywuzzy`) with customizable confidence thresholds to detect variant spellings, typos, and masked words.
* **Multi-Category Risk Lexicon:** Pre-configured dictionaries targeting forensic investigative categories:
* Abuse and Harassment
* Physical Threats and Violence
* Self-Harm and Depression
* Explicit / Sexual Exploitation
* Financial Fraud and Credential Harvesting
* Illicit Substance References


* **Actor Attribution:** Generates frequency matrices of flagged content per sender for forensic evidentiary reporting.

---

## Forensic Detection Categories

The lexical analysis engine screens extracted tokens against categorized risk profiles:

| Category | Target Indicators | Forensic Objective |
| --- | --- | --- |
| **Abuse** | Profanity, personal insults, targeted hostility | Workplace harassment, cyberbullying investigations |
| **Threats** | Violent intent, physical harm, intimidation | Criminal investigations, threat intelligence |
| **Depression** | Suicidal ideation, hopelessness, self-harm signals | Welfare checks, behavioral health risk assessment |
| **Sexual** | Explicit terms, harassment, non-consensual language | Harassment, exploitation, policy violation analysis |
| **Financial** | Account details, PINs, OTPs, UPI, banking terms | Social engineering, credential theft, fraud audits |
| **Drugs** | Narcotics, controlled substances, trade slang | Contraband trafficking, illicit activity tracking |

---

## Prerequisites and Dependencies

The toolkit requires Python 3.8+ and the following analytical libraries:

* `pandas` - Data manipulation and time-series extraction
* `matplotlib` & `seaborn` - Chronological and categorical visualizations
* `nltk` - Tokenization and text preprocessing (`punkt`, `stopwords`)
* `fuzzywuzzy` & `python-Levenshtein` - Approximate string matching acceleration

---

## Installation

Install the required packages using `pip`:

```bash
pip install pandas matplotlib seaborn nltk fuzzywuzzy python-Levenshtein

```

If deploying within Google Colab, execute:

```python
!pip install fuzzywuzzy[speedup] nltk pandas matplotlib seaborn

```

---

## Data Format Requirements

WhatsApp export formats vary by region, operating system, and system locale. This script is calibrated for export timestamps formatted as:

```text
DD/MM/YYYY, HH:MM AM/PM - Sender Name: Message Content

```

*Example target line:*

```text
26/09/2024, 01:45 PM - John Doe: Review the document attached.

```

If your chat exports use 24-hour military time, dashes (`-`), or alternate date conventions (e.g., `YYYY-MM-DD` or `MM/DD/YY`), update the parsing regular expression:

```python
# Default pattern (12-hour AM/PM format)
pattern = r"(\d{2}/\d{2}/\d{4}), (\d{1,2}:\d{2} (?:AM|PM)) - ([^:]+): (.+)"

# Example 24-hour bracketed format: "[DD/MM/YY, HH:MM:SS] Sender: Message"
# pattern = r"\[(\d{2}/\d{2}/\d{2}), (\d{2}:\d{2}:\d{2})\] ([^:]+): (.+)"

```

---

## Methodology

### 1. Ingestion and Normalization

Raw logs are converted line-by-line via regular expressions into a tabular format. Invalid lines, media-omitted metadata lines, and multi-line message breaks are handled prior to datetime indexation.

### 2. Temporal & Behavioral Analysis

Timestamps are cast into native pandas `datetime64[ns]` objects, enabling rolling averages, daily delta calculations, and peak message volume identification.

### 3. Tokenization & Heuristic Matching

Messages undergo lexical analysis through the following pipeline:

1. Punctuation removal and lowercasing.
2. Extraction of alphabetical tokens exceeding 2 characters.
3. Stopword removal using NLTK's English corpus.
4. Calculation of `fuzz.partial_ratio(token, keyword)` against the categorized risk lexicon. A threshold of `95%` is applied by default to balance precision against false-positive evasion detection.

---

## Outputs and Visualizations

Running the analysis produces the following forensic artifacts:

1. **Activity Timeline (`Messages per Day`):** Chronological distribution identifying conversational surges, drop-offs, and critical evidentiary dates.
2. **Actor Volume Chart (`Top Active Senders`):** Identification of central actors based on baseline participation.
3. **Keyword Frequency Distribution (`Top 15 Most Used Offensive Words`):** Aggregation of illicit terminology to assess predominant threat types.
4. **Risk Attribution Chart (`Top Users by Offensive Word Usage`):** Direct attribution linking categorized flagged vocabulary to specific telephone numbers or contact handles.

---

## Ethical and Legal Notice

This software is developed strictly for authorized digital forensics, academic research, corporate internal investigations, and incident response operations. Ensure full legal authorization, consent, or jurisdictional compliance before intercepting or analyzing private communications.
