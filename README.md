# AMHotSpot
Multi-source per-residue amyloid hotspot prediction

# AMHotSpot: A Multi-Source Attention-Based Framework for Per-Residue Amyloid Hotspot Prediction

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-orange.svg)](https://pytorch.org/)
- [ESM-2](https://github.com/facebookresearch/esm) — Meta AI protein language model
- [IUPred3](https://iupred.elte.hu/) — Intrinsic disorder prediction
- [AlphaFold](https://alphafold.ebi.ac.uk/) — Protein structure prediction
- [AmyPro](http://amypro.net) — Amyloid protein database

## Overview

AMHotSpot  is a deep learning framework for per-residue amyloid hotspot prediction that integrates five complementary biological modalities through a cross-source attention mechanism:

- ESM-2 : protein language model embeddings (sequence context)
- IUPred3 : intrinsic disorder scores (conformational flexibility)
- AlphaFold :  structural confidence (pLDDT) 
 - DSSP solvent accessibility  
- SHAP : selected terminal physicochemical features

## Architecture
- Parameters: 3,997,830
- Threshold: τ = 0.35
- Max sequence length: 512
- Hidden dimension: 256

## Dataset
- Positive training proteins from AmyPro database
- Negative proteins from ECAmyloid server

## Installation
bash
git clone https://github.com/hbzy-amyl/AMHotSpot.git
cd AMHotSpot

pip install -r requirements.txt

### Requirements
```
torch>=2.0.0
transformers>=4.30.0
numpy>=1.24.0
scikit-learn>=1.2.0
matplotlib>=3.7.0
biopython>=1.81
shap>=0.41.0
xgboost>=1.7.0
pymol-open-source>=2.5.0   # optional, for 3D ribbon figures
pdf2image>=1.16.0           # optional, for panel figures
Pillow>=9.0.0
img2pdf>=0.4.0
```
## Usage

### Prediction

bash
python3 amhotspot.py \
    --fasta     proteins.fasta \
    --out_dir   results/ \
    --threshold 0.35
    
### Per-Residue Profile Plot
bash
python3 plot_hotspot_profile.py \
    --json      results/test_predictions.json \
    --fasta     test.fasta \
    --ap_id     AP00162 AP00133 \
    --threshold 0.35 \
    --out       results/hotspot_profile.pdf


### Gate Weight Analysis
bash
python3 plot_gate_weights.py \
    --predictions results/test_predictions.json \
    --fasta       test.fasta \
    --out         results/gate_weights.pdf

### 3D Ribbon Visualization (requires PyMOL)
bash
python3 render_pymol.py \
    --json    results/test_predictions.json \
    --fasta   test.fasta \
    --pdb_dir af_pdbs/ \
    --ap_id   AP00162 AP00133 \
    --out_dir results/pymol_figures/

## FASTA Format
Input FASTA files follow the AmyPro annotation format:
>AP00162 (Homo sapiens) uniprot=Q9Y572 pdbs=5ZCK pmid={22817896} category=functional amyloid | hotspots=70-75
MKKK...
```
Hotspot positions are 0-based in the FASTA header.

## SHAP Analysis
----------------
Terminal physicochemical features are selected via SHAP-based importance ranking. 
The top features (by cumulative importance) include:

bash
python3 shap_selection.py \
    --fasta      train.fasta \
    --n_terminal 10 \
    --out        results/shap_analysis.pdf


## Repository Structure : 
AMHotSpot/
├── multimodal_hotspot_pipeline.py   # Main pipeline
├── plot_hotspot_profile.py          # Per-residue profile plots
├── plot_roc_curves.py               # ROC + PR + F1 curves
├── loocv_analysis.py                # LOOCV analysis & figures
├── plot_gate_weights.py             # Gate weight analysis
├── shap_selection.py                # SHAP feature selection
├── render_pymol.py                  # 3D ribbon visualization
├── train.fasta                      # Training set
├── test.fasta                       # Test set
├── af_pdbs/                         # AlphaFold PDB files
├── results/
│   ├── multimodal_model.pt          # Trained model weights
│   ├── train_predictions.json       # Training predictions
│   ├── test_predictions.json        # Test predictions
│   └── multimodal_model_loocv.json  # LOOCV results
└── requirements.txt

## Availability

Code and datasets will be made publicly available
upon acceptance of the manuscript.

## Citation
If you use AMHotSpot in your research, please cite:
bibtex
@article{ ,
  title   = {{AMHotSpot}: A Multi-Source Attention-Based Framework
             for Per-Residue Amyloid Hotspot Prediction},
  author  = {Zohra Yagoub and Hafida Bouziane},
  journal = {},
  year    = {},
  doi     = {}
}
