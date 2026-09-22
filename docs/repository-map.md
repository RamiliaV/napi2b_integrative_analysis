# Repository map

## Purpose of this map

This document explains how to navigate the repository and what each major component is responsible for. It is meant for readers who need orientation before inspecting the full notebooks.

## Main components

### `README.md`

The main landing page of the repository. It should contain the project motivation, the biological question, a simplified pipeline diagram, and 2 to 3 curated figures.

### `notebooks/`

This folder contains the core scientific notebooks.

- `Phase3A_preprocessing_scientific_v5.6.ipynb` — preprocessing, export generation, structural features, contact extraction, heterogeneous graph preparation, and QC panels.
- `Phase3B_Topological_Graph_Analysis_v6.ipynb` — consensus graph construction, graph QC, centrality, regional extracellular analysis, community detection, temporal graph analysis, and publication-style figure generation.

### `docs/`

This folder contains short supporting documents that help readers understand the project without reading code cell by cell.

Recommended files:

- `project-overview.md` — scientific and methodological overview.
- `data-contract.md` — description of the interface between Phase3A exports and Phase3B inputs.
- `repository-map.md` — this file.
- `figures.md` — optional list of key illustrations and captions for quick browsing.

### `docs/figures/`

This folder should contain curated figures for onboarding and presentation. It is better to store a small number of explanatory, publication-quality panels here than to expose many intermediate notebook plots.

Suggested contents:

- `pipeline_overview.png` — schematic of the full project workflow.
- `phase3a_exports_scheme.png` — visualization of Phase3A outputs and their role downstream.
- `qc_normal_tumor.png` — preprocessing QC comparison.
- `epitope_graph_figure.png` — regional extracellular or epitope topology figure.
- `community_alluvial.png` — community reshuffling between conditions.
- `temporal_rin.png` — temporal graph metrics over trajectory windows.

## Pilot ST-GNN artifacts

### Notebook

- `notebooks/ST_GNN_Pilot2.ipynb`  
  End-to-end pilot workflow: artifact loading, graph construction,
  autoencoder training, latent-space analysis, ST-GNN evaluation,
  baselines, and permutation control.

### Results

- `results/figures/ae_training_curves.png`
- `results/figures/umap_latent_space.png`
- `results/figures/stgnn_real_vs_permutation.png`

- `results/tables/stgnn_results_real_per_fold_seed.csv`
- `results/tables/stgnn_summary_bootstrap_ci.csv`
- `results/tables/final_comparison_table.csv`

- `results/manifests/stgnn_pilot_blocked_temporal_manifest.json`
