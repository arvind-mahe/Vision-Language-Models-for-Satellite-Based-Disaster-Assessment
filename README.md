# Vision–Language Model for Satellite-Based Disaster Assessment

> **FSE 570 Data Science Capstone** — Team Pennsylvania  
> Arizona State University

An end-to-end pipeline that converts paired pre/post-disaster satellite imagery into structured, grounded situation reports — enabling faster triage and better resource allocation for emergency responders.

---

## The Problem

Natural disasters demand rapid damage assessment, but existing computer vision outputs (maps, labels) still require manual expert interpretation before they become decision-ready. This project closes that gap.

**Pipeline output:** building-level damage classifications → structured JSON facts → natural-language situation report.

---

## Team

| Name | Role | Email |
|------|------|-------|
| Raaghul Nataraj Kannan | Lead | rkanna18@asu.edu |
| Yashwanth Jawaji | Deputy | yjawaji@asu.edu |
| Arvind Mahendran | Member | amahend6@asu.edu |
| Sashank Donavalli | Member | sdonava1@asu.edu |
| Nanda Gopal Chalasani | Member | nchalas4@asu.edu |

---

## Architecture

```
Pre/Post Satellite Images
        │
        ▼
┌───────────────────┐
│  Building Cropper │  ← xBD polygons, 6-channel input (RGBpre + RGBpost)
└────────┬──────────┘
         │
         ▼
┌────────────────────┐
│  Damage Classifier │  ← ResNet18 → ResNet50/EfficientNet/ViT
│  (no-damage / minor / major / destroyed)
└────────┬───────────┘
         │
         ▼
┌─────────────────────┐
│  Scene Aggregator   │  ← JSON: counts, severe ratio, hotspots
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Report Generator   │  ← LLM + constrained prompts + grounding checks
└─────────────────────┘
         │
         ▼
  Situation Report (natural language)
```

---

## Datasets

| Dataset | Role | Details |
|---------|------|---------|
| **xView2 (xBD)** | Primary — train & eval | Paired pre/post RGB imagery, building polygons, 4-class damage labels |
| **SpaceNet 8** | Generalization testing | High-res imagery with building footprints & road labels |
| **OpenStreetMap** | Context enrichment *(optional)* | Roads, hospitals, shelters for prioritization |

---

## Repository Structure

```
Vision-Language-Models-for-Satellite-Based-Disaster-Assessment/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   ├── xview2-2.ipynb            # xBD dataset — preprocessing, training, evaluation
│   └── SpaceNet8_evaluation.ipynb  # SpaceNet 8 generalization + label mapping
│
└── src/                          # Cleaned Python modules (in progress)
    ├── data_preprocessing.py
    ├── train_xview2_model.py
    ├── evaluate_spacenet8.py
    ├── report_generation.py
    └── utils.py
```

---

## Setup

```bash
# 1. Clone the repo
git clone https://github.com/arvind-mahe/Vision-Language-Models-for-Satellite-Based-Disaster-Assessment.git
cd Vision-Language-Models-for-Satellite-Based-Disaster-Assessment

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # Mac/Linux
# venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt
```

> **Note:** Datasets, trained model weights, and generated outputs are not included in this repo. Download them separately and place locally (see `.gitignore` for excluded paths).

---

## Methodology

Development follows a tiered plan to ensure deliverability while allowing up-scoping.

**Bronze — Building-level damage classifier**
- 6-channel input stacking (RGBpre + RGBpost) with context padding and min crop size
- ResNet18 baseline → ResNet50 / EfficientNet / lightweight ViT
- Augmentation: flips, color jitter, blur/noise; class balancing via weighted loss + balanced sampling
- Strict scene-level train/val splits to prevent leakage

**Silver — Grounded situation report generation**
- JSON aggregation: per-class counts, severe damage ratio (major + destroyed), hotspots via grid clustering
- LLM report generation constrained to JSON facts — minimizes hallucination
- Automatic grounding checks: numeric consistency between report text and JSON

**Gold — Prioritization & generalization**
- DBSCAN clustering of severely damaged buildings; zone ranking by severity density
- OSM proximity features (roads, hospitals/shelters) for decision-oriented output
- SpaceNet integration for cross-dataset generalization testing

---

## Evaluation

| Component | Metrics |
|-----------|---------|
| Damage classifier | Macro-F1, per-class precision/recall/F1, confusion matrix |
| Robustness | Per disaster type and region breakdowns |
| Report generation | Fact mismatch rate (JSON ↔ text); ROUGE/BLEU as secondary |

---

## Project Timeline

| Date | Milestone |
|------|-----------|
| 02/12 | Proposal submitted; scope finalized |
| 02/19 | Datasets downloaded; preprocessing & splits complete |
| 03/05 | Bronze baseline trained; metrics reported |
| 03/19 | Silver prototype: JSON aggregation + report generation end-to-end |
| 04/02 | Ablations (crop/backbone/augmentation); robustness evaluation |
| 04/23 | Full pipeline demo + report draft |
| 04/30 | Final submission |

---

## Ethical Considerations

This system is **decision support**, not authoritative damage certification. All outputs are model-driven estimates and should be used alongside human expertise. Known limitations include performance variation by disaster type, cloud/smoke occlusion, and potential distribution shift across geographic regions.

---

## License

For academic use only — FSE 570 Capstone Project, Arizona State University.
