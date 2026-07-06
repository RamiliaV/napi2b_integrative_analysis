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

## Suggested reading paths

### For a scientist

1. `README.md`.
2. `docs/project-overview.md`.
3. `docs/figures/` panels.
4. `Phase3B_Topological_Graph_Analysis_v6.ipynb` for the biological interpretation layer.
5. `Phase3A_preprocessing_scientific_v5.6.ipynb` for low-level preprocessing details.

### For a developer

1. `README.md`.
2. `docs/data-contract.md`.
3. `Phase3A_preprocessing_scientific_v5.6.ipynb` to inspect exported artifact logic.
4. `Phase3B_Topological_Graph_Analysis_v6.ipynb` to inspect downstream assumptions and figure generation.

### For a supervisor or reviewer

1. `README.md`.
2. Pipeline overview figure.
3. One QC figure.
4. One core scientific result figure from Phase3B, preferably extracellular topology or community reshuffling.

## Recommended future cleanup

As the project matures, the repository would benefit from separating raw results, curated figures, reusable helper code, and manuscript-oriented assets into clearer folders. Even without full refactoring, a well-written `README.md` and the three short documents above already make the project substantially easier to understand for a new reader.