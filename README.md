# Vision-Language Model for Satellite-Based Disaster Assessment

## Overview

This project focuses on satellite-based disaster damage assessment using pre-disaster and post-disaster imagery. The goal is to classify building-level damage, evaluate cross-dataset generalization, and generate structured disaster assessment outputs.

## Current Source Code

The current repository contains two main notebooks:

1. `xview2-2.ipynb`  
   - Works with the xView2/xBD dataset
   - Handles pre-disaster and post-disaster satellite imagery
   - Supports building-level disaster damage assessment experiments

2. `SpaceNet8_evaluation.ipynb`  
   - Evaluates generalization using SpaceNet 8
   - Includes road/context enrichment helpers
   - Maps SpaceNet 8 flood labels into an xBD-style damage schema

## Repository Structure

```text
notebooks/
    xview2-2.ipynb
    SpaceNet8_evaluation.ipynb

src/
    Future cleaned Python source files
