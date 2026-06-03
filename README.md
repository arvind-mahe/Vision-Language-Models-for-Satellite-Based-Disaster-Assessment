# Vision-Language Models for Satellite-Based Disaster Assessment

## Overview

This project implements a three-tier disaster assessment pipeline that converts paired pre-disaster and post-disaster satellite imagery into structured, decision-ready situation reports.

The system combines computer vision, geospatial data processing, structured JSON aggregation, and schema-grounded report generation to support post-disaster response planning. The pipeline was evaluated using the xView2/xBD satellite damage dataset, SpaceNet8 flood imagery, and OpenStreetMap road-network data.

## Project Motivation

Rapid disaster assessment is critical for emergency response, insurance analysis, and recovery planning. Manual review of satellite imagery is slow, and free-form language model outputs can introduce hallucinated or unverifiable information.

This project addresses that challenge by separating the workflow into three auditable stages:

1. **Bronze Tier**: Building-level damage classification from satellite imagery
2. **Silver Tier**: Scene-level JSON aggregation and schema-grounded report generation
3. **Gold Tier**: Geospatial enrichment using OpenStreetMap and OSMnx

## Pipeline Architecture

```text
Pre/Post Satellite Tiles
        +
Building Polygons
        |
        v
Bronze Tier
9-channel ResNet-18 damage classifier
        |
        v
Scene-Level JSON Records
damage counts, severe ratio, hotspots
        |
        v
Gold Tier
OSMnx road-network enrichment
        |
        v
Silver Tier
Schema-grounded situation reports
```

## Datasets

### xView2 / xBD

The primary dataset used in this project is xView2/xBD, which provides paired pre-disaster and post-disaster satellite imagery with building polygon annotations and four damage labels:

* No damage
* Minor damage
* Major damage
* Destroyed

The project processed and evaluated **53,137 held-out building crops** from the xBD hold-out split.

### SpaceNet8

SpaceNet8 was used for cross-dataset flood-transfer evaluation. The project evaluated a binary flood-transfer setup on **3,986 SpaceNet8 buildings**.

### OpenStreetMap

OpenStreetMap data was accessed through OSMnx to enrich disaster scenes with road-network and access-related features.

## Key Features

* Processed paired pre-disaster and post-disaster satellite imagery
* Generated building-level crops from polygon annotations
* Constructed structured scene-level JSON records
* Aggregated damage counts and severe-damage ratios
* Added DBSCAN-based priority hotspot detection
* Integrated OSMnx road-network enrichment
* Computed road density, access-risk signals, and nearest-hospital context
* Generated schema-grounded situation reports with provenance tracking
* Validated numeric claims using mismatch-rate checking

## Model and Methodology

The Bronze tier uses a **9-channel ResNet-18** model. The input contains:

```text
Pre-disaster RGB image
+
Post-disaster RGB image
+
Absolute pre/post difference image
```

This creates a 9-channel input representation:

```text
pre RGB | post RGB | absolute difference
```

The model was trained for building-level disaster damage classification. The downstream scene-level outputs were then aggregated into JSON records for report generation and decision-support enrichment.

## Results

### xBD Damage Classification

The Bronze-tier model achieved the following results on **53,137 held-out xBD building crops**:

| Metric      | Result |
| ----------- | -----: |
| Accuracy    |  0.842 |
| Macro-F1    |  0.739 |
| Weighted F1 |  0.851 |

### SpaceNet8 Zero-Shot Flood Transfer

The model was also evaluated on **3,986 SpaceNet8 buildings** for binary flood-transfer evaluation.

| Class       | Precision | Recall |    F1 |
| ----------- | --------: | -----: | ----: |
| Flooded     |     0.417 |  0.092 | 0.151 |
| Non-flooded |     0.694 |  0.941 | 0.799 |

### Silver-Tier Grounded Reports

The Silver-tier schema-grounded reporting pipeline achieved:

```text
0.0% numeric mismatch rate on three demo scenes
```

This means every numeric value cited in the generated reports was traceable back to a valid field in the source JSON record.

### Gold-Tier Geospatial Enrichment

The Gold tier was evaluated on **30 cross-disaster scenes** spanning ten disaster sub-types. OSMnx was used to enrich scene records with:

* Road density
* Nearest-hospital context
* Access-risk signals
* DBSCAN-based priority hotspots
* Scene-level priority labels

## Repository Structure

```text
.
├── README.md
├── requirements.txt
├── notebooks/
│   ├── xview2-2.ipynb
│   └── SpaceNet8_evaluation.ipynb
├── src/
│   ├── preprocessing/
│   ├── aggregation/
│   ├── geospatial/
│   ├── reporting/
│   └── evaluation/
├── outputs/
│   ├── scene_json/
│   ├── reports/
│   └── figures/
└── app/
    └── streamlit_app.py
```

## Current Source Code

The current repository contains the main project notebooks:

```text
notebooks/xview2-2.ipynb
notebooks/SpaceNet8_evaluation.ipynb
```

These notebooks include the main experimental workflows for xView2/xBD processing, SpaceNet8 evaluation, and project pipeline development.

## Setup Instructions

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate the environment.

For Windows:

```bash
venv\Scripts\activate
```

For macOS or Linux:

```bash
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Requirements

Example Python dependencies:

```text
numpy
pandas
matplotlib
scikit-learn
opencv-python
Pillow
tqdm
torch
torchvision
rasterio
shapely
geopandas
osmnx
networkx
streamlit
jupyter
```

## Important Security Note

API keys and secrets should not be committed to this repository.

Use environment variables for API keys:

```python
import os

GOOGLE_API_KEY = os.getenv("GOOGLE_API_KEY")
```

Store local secrets in a `.env` file and make sure `.env` is included in `.gitignore`.

## Reproducibility Notes

Large satellite datasets, trained model weights, generated outputs, and API keys are not included in this repository. Users must configure local dataset paths before running the notebooks.

Recommended ignored folders:

```text
data/
datasets/
outputs/
models/
checkpoints/
.env
```

## Project Contributions

This project was developed as part of a graduate capstone project at Arizona State University.

My main contributions focused on:

* Data preprocessing for paired satellite imagery
* Building crop and scene-record preparation
* Gold-tier OSMnx geospatial integration
* Road-network enrichment for disaster decision support

## Future Work

Potential improvements include:

* Converting notebook workflows into modular Python scripts
* Adding automated data validation checks
* Improving cross-dataset flood-transfer performance
* Adding GSD-aware physical-scale cropping
* Expanding the Streamlit dashboard for interactive disaster-scene analysis
* Adding CI checks for schema validation and numeric mismatch rate

## License

This repository is intended for academic and research use.
