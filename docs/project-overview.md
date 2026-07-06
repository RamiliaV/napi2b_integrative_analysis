# Project overview

## Purpose

This project studies the NaPi2b transporter (SLC34A2) in normal and tumor-like environments using a combined molecular dynamics and graph-analysis framework. The central idea is that tumor-associated changes in microenvironment may alter not only global structural behavior, but also the local topology of extracellular and epitope-associated interaction networks.

## Scientific rationale

NaPi2b is relevant as a membrane protein of biomedical interest, especially in the context of ovarian cancer and extracellular targeting. Classical molecular dynamics descriptors such as RMSD, RMSF, and radius of gyration are informative for global motion and local flexibility, but they do not necessarily capture hidden rewiring of residue-level communication patterns. To address this, the project transforms trajectory-derived contacts into residue interaction networks and analyzes them using graph topology, regional subnetwork analysis, and temporal network summaries.

## Analytical logic

The workflow is organized into two major stages.

**Phase3A** performs preprocessing and feature extraction from molecular dynamics data. This stage standardizes residue-level features, protein-protein contacts, lipid and glycan context, heterograph representations, and time-resolved exports needed for downstream analysis.

**Phase3B** takes these outputs and performs topological analysis of condition-specific residue interaction networks. This includes graph quality control, consensus graph construction, centrality analysis, extracellular domain and epitope-focused analysis, community detection, allosteric path exploration, and temporal residue interaction network analysis.

## What makes the repository useful

The repository is intended not only as a storage space for notebooks, but as a structured scientific workspace. It makes the relationship between preprocessing, graph construction, and biological interpretation explicit, which is important for reproducibility, collaboration, and later packaging into a more formal pipeline or service.

## Who this repository is for

This repository should be understandable to several types of readers:

- structural bioinformatics researchers who want to understand the scientific workflow,
- collaborators who need a high-level map of the project before opening the notebooks,
- developers who may later package parts of the workflow into a reusable pipeline or web interface.

## Suggested reading order

1. Start with `README.md` for the project scope and key figures.
2. Continue with `docs/repository-map.md` to understand the folder structure and notebook roles.
3. Open `Phase3A_preprocessing_scientific_v5.6.ipynb` to inspect preprocessing logic and exported artifacts.
4. Then open `Phase3B_Topological_Graph_Analysis_v6.ipynb` to see graph construction, topology analysis, and figure generation.