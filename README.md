# TherapyInSimulation

This repository contains transcript annotations, computational discourse analyses, and derived results for simulated psychotherapy dialogues. The project examines how linguistic behavior changes across baseline interaction, alliance rupture, and repair, and how these changes relate to interpersonal ratings such as affiliation and dominance.

The analysis notebook uses [ELFen](https://github.com/gsarti/elfen) and common Python data-science libraries to extract turn-level linguistic features, summarize phase shifts, and generate publication-ready figures and tables.

> **Research status:** This is an analysis repository for simulated-patient research. Its outputs are descriptive and exploratory; they are not clinical assessments or diagnostic tools.

## Repository structure

Results are organized by simulated patient. A typical checkout looks like this:

```text
.
├── code/
│   └── run_analysis.ipynb
├── data/
│   ├── SimulatedPatient1/
│   │   ├── SP1_transcript_psych_discourse_ratings.json
│   │   └── SP1_transcript_psych_discourse_ratings/
│   │       ├── discourse_annotation_audit.csv
│   │       ├── discourse_by_clinical_state.csv
│   │       ├── discourse_by_speaker.csv
│   │       ├── discourse_turn_data.csv
│   │       ├── full_discourse_analysis_output.csv
│   │       ├── linguistic_annotation_correlations.csv
│   │       ├── linguistic_shifts_summary.csv
│   │       ├── rupture_response_windows.csv
│   │       ├── sequential_response_summary.csv
│   │       └── therapist_patient_pairs.csv
│   └── SimulatedPatient2/
│       └── ...
└── README.md
```

The shared notebook is stored in `code/`. Each `data/SimulatedPatient*` directory contains one annotated JSON transcript and a generated results directory with the same name as the transcript stem. The generated directory contains the CSV tables and figures for that patient.

## Analysis workflow

The notebook performs the following steps:

1. Loads a JSON transcript and its human annotations into a pandas DataFrame.
2. Normalizes column names and interaction-state labels.
3. Extracts linguistic features with ELFen and spaCy.
4. Computes sequential measures, including patient turn length relative to the preceding therapist turn.
5. Compares patient language across baseline, rupture, and repair phases.
6. Relates linguistic features to affiliation and dominance ratings.
7. Exports analysis tables and publication-quality figures.

The analysis distinguishes these interaction states:

- `Baseline`
- `Withdrawal`
- `Confrontation`

## Input data

The transcript files committed to this repository are JSON files, for example:

```text
SP1_transcript_psych_discourse_ratings.json
SP2_transcript_psych_discourse_ratings.json
```

They use a record-oriented structure: each JSON object represents one dialogue turn. Load a transcript directly into a pandas DataFrame with:

```python
from pathlib import Path
import pandas as pd

transcript_path = Path(
    "data/SimulatedPatient1/"
    "SP1_transcript_psych_discourse_ratings.json"
)
df = pd.read_json(transcript_path)
```

The analysis expects the following information:

| Required information | Accepted or expected column names |
| --- | --- |
| Speaker | `speaker` or `role` |
| Turn text | A name containing `transcript`, `transcipt`, or `text` |
| Rupture category | A name containing `rupture typ` |
| Affiliation rating | A name containing `affiliation` |
| Dominance rating | A name containing `dominance` |
| Rupture strength | A name containing `rupture strength` |
| Discourse uptake | A name containing `uptake` |
| Elaboration | A name containing `elaboration` |
| Grounding moves | A name containing `grounding moves` |
| Metacommunication | A name containing `metacommunication` |
| Relevance | A name containing `relevance` |

Speaker values should be `Patient` or `Therapist`. Affiliation and dominance ratings must lie between `-1` and `1` when present.

Recognized rupture labels include baseline/none, withdrawal, confrontation, repair, resolution, and metacommunication. Several spelling variants are normalized automatically. Unexpected labels cause the notebook to stop rather than silently reclassify the data.

## Installation

Python 3.11 is recommended. Create and activate a virtual environment:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
```

Install the analysis dependencies:

```bash
python -m pip install \
  jupyter \
  pandas \
  numpy \
  polars \
  matplotlib \
  seaborn \
  spacy \
  wn \
  elfen

python -m spacy download en_core_web_sm
```

ELFen may require additional lexical resources for some features. Follow the ELFen documentation for the version you install and ensure its resources are available before running the full extraction.

The notebook looks for WordNet data in `wn_data/` at the current working directory by default. To use another location, set `WN_DATA_DIR` before starting Jupyter:

```bash
export WN_DATA_DIR="/path/to/wn_data"
jupyter lab
```

The `wn_data/` directory should normally be excluded from Git if it contains downloaded resources:

```gitignore
wn_data/
```

## Running an analysis

1. Activate the environment and start Jupyter:

   ```bash
   source .venv/bin/activate
   jupyter lab
   ```

2. Open `code/run_analysis.ipynb`.

3. In the data-loading cell, select the JSON transcript for the simulated patient being analyzed:

   ```python
   PROJECT_ROOT = Path.cwd()

   # Jupyter commonly starts the kernel in the notebook's code/ directory.
   if PROJECT_ROOT.name == "code":
       PROJECT_ROOT = PROJECT_ROOT.parent

   transcript_path = (
       PROJECT_ROOT
       / "data"
       / "SimulatedPatient1"
       / "SP1_transcript_psych_discourse_ratings.json"
   )
   ```

4. Make sure `WN_DATA_DIR` is set or a local `wn_data/` directory exists.

5. Run the notebook from top to bottom.

The notebook loads the JSON file with `pd.read_json()`, validates the annotations, and creates an output directory beside the transcript. For Patient 1, the generated files are written to:

```text
data/SimulatedPatient1/SP1_transcript_psych_discourse_ratings/
```

## Main outputs

The current repository includes these analysis tables:

| File | Contents |
| --- | --- |
| `discourse_turn_data.csv` | Turn-level discourse measures and annotations |
| `full_discourse_analysis_output.csv` | Complete turn-level feature extraction output |
| `discourse_by_clinical_state.csv` | Measures summarized by interaction state |
| `discourse_by_speaker.csv` | Measures summarized by speaker |
| `linguistic_shifts_summary.csv` | Standardized changes from baseline to rupture and repair |
| `linguistic_annotation_correlations.csv` | Feature correlations with affiliation and dominance ratings |
| `therapist_patient_pairs.csv` | Adjacent therapist-patient turn pairs |
| `rupture_response_windows.csv` | Local response windows around rupture events |
| `sequential_response_summary.csv` | Aggregated sequential response measures |
| `discourse_annotation_audit.csv` | Annotation coverage and consistency checks |
| `figure1_plot_data.csv` | Data used for the linguistic phase-shift figure |
| `figure1_individual_turn_data.csv` | Turn-level values shown in the phase analysis |
| `figure2_plot_data.csv` | Affiliation and dominance coordinates used in the circumplex |
| `figure4_plot_data.csv` | Data used for the discourse-profile figure |
| `figure5_plot_data.csv` | Data used for the sequential-uptake figure |

The notebook generates five figures:

1. Linguistic shifts from baseline to rupture and repair.
2. Interpersonal positions and turn sequence in the affiliation-dominance circumplex.
3. Sequential rupture-repair dynamics.
4. Patient discourse features by clinical state.
5. Patient uptake following therapist metacommunication.

## Reproducibility notes

- Run all cells in order. Later cells depend on variables created during loading and feature extraction.
- Keep the shared notebook in `code/` and select a case by changing `transcript_path` to the corresponding file under `data/`.
- The current notebook still imports `openpyxl`, although the JSON workflow does not require it; that unused import can be removed.
- WordNet data are configured through `WN_DATA_DIR`, with `./wn_data` as the default.
- Standardized phase shifts can be unstable when only a few baseline patient turns are available. They should not be interpreted as significance tests.
- Generated CSV and figure files may be overwritten when the notebook is rerun with the same input filename.

## Ethical and privacy considerations

This project concerns psychotherapy dialogue. Even when working with simulated patients, avoid committing personal identifiers, confidential clinical material, API keys, or unrestricted source transcripts. Review all data before making a repository public.

## Citation

If you use this repository in academic work, cite the accompanying publication or project documentation. *A formal citation can be added here when the publication details are finalized.*

