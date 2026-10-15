# TherapyInSimulation

This repository contains transcript annotations, computational discourse analyses, and derived results for simulated psychotherapy dialogues. The project examines how linguistic behavior changes across baseline interaction, alliance rupture, and repair, and how these changes relate to interpersonal ratings such as affiliation and dominance.

The analysis notebook uses [ELFen](https://github.com/gsarti/elfen) and common Python data-science libraries to extract turn-level linguistic features, summarize phase shifts, and generate publication-ready figures and tables.

> **Research status:** This is an analysis repository for simulated-patient research. Its outputs are descriptive and exploratory; they are not clinical assessments or diagnostic tools.

## Repository structure

Results are organized by simulated patient. A typical checkout looks like this:

```text
.
├── SimulatedPatient1/
│   ├── run_discourse_analysis.ipynb
│   ├── SP1_transcript_psych_discourse_ratings.json
│   ├── discourse_annotation_audit.csv
│   ├── discourse_by_clinical_state.csv
│   ├── discourse_by_speaker.csv
│   ├── discourse_turn_data.csv
│   ├── full_discourse_analysis_output.csv
│   ├── linguistic_annotation_correlations.csv
│   ├── linguistic_shifts_summary.csv
│   ├── rupture_response_windows.csv
│   ├── sequential_response_summary.csv
│   └── therapist_patient_pairs.csv
├── SimulatedPatient2/
│   └── ...
└── README.md
```

Each `SimulatedPatient*` directory is a self-contained analysis case. The JSON file contains the annotated transcript or ratings associated with that case, while the CSV files contain turn-level and aggregated results.

## Analysis workflow

The notebook performs the following steps:

1. Loads a transcript and its human annotations from Excel.
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
- `Repair`

## Input data

The notebook currently reads an `.xlsx` workbook with one dialogue turn per row. It recognizes common variants of the following columns:

| Required information | Accepted or expected column names |
| --- | --- |
| Speaker | `speaker` or `role` |
| Turn text | A name containing `transcript`, `transcipt`, or `text` |
| Rupture category | A name containing `rupture typ` |
| Affiliation rating | A name containing `affiliation` |
| Dominance rating | A name containing `dominance` |
| Rupture strength | A name containing `rupture strength` |

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
  openpyxl \
  spacy \
  wn \
  elfen

python -m spacy download en_core_web_sm
```

ELFen may require additional lexical resources for some features. Follow the ELFen documentation for the version you install and ensure its resources are available before running the full extraction.

## Running an analysis

1. Activate the environment and start Jupyter:

   ```bash
   source .venv/bin/activate
   jupyter lab
   ```

2. Open `run_discourse_analysis.ipynb` in the relevant simulated-patient directory.

3. In the data-loading cell, replace the hard-coded input path with the path to your annotated workbook:

   ```python
   excel_file_path = "path/to/annotated_transcript.xlsx"
   ```

4. If WordNet data is required, replace the machine-specific path with the location on your system:

   ```python
   import os
   os.environ["WN_DATA_DIR"] = "path/to/wn_data"
   ```

5. Run the notebook from top to bottom.

The notebook creates an output directory beside the workbook, using the workbook filename without its extension. For example:

```text
annotated_transcript.xlsx
annotated_transcript/
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

Depending on the notebook version, the generated figures may include:

- linguistic phase shifts;
- an interpersonal circumplex trajectory; and
- a sequential rupture-repair timeline.

## Reproducibility notes

- Run all cells in order. Later cells depend on variables created during loading and feature extraction.
- The notebook currently installs `openpyxl` from inside its first cell. For a reproducible environment, install dependencies before launching Jupyter and remove or skip that installation line.
- The input workbook and WordNet locations are currently machine-specific and must be changed on a new system.
- Standardized phase shifts can be unstable when only a few baseline patient turns are available. They should not be interpreted as significance tests.
- Generated CSV and figure files may be overwritten when the notebook is rerun with the same input filename.

## Ethical and privacy considerations

This project concerns psychotherapy dialogue. Even when working with simulated patients, avoid committing personal identifiers, confidential clinical material, API keys, or unrestricted source transcripts. Review all data before making a repository public.

## Citation

If you use this repository in academic work, cite the accompanying publication or project documentation. *A formal citation can be added here when the publication details are finalized.*

