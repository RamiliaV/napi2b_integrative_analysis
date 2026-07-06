# NaPi2b Structural Bioinformatics Project

A structural bioinformatics project focused on the NaPi2b transporter (SLC34A2), combining molecular dynamics, residue interaction networks, and graph-based topological analysis to study microenvironment-dependent remodeling in normal and tumor-like conditions.

## Overview

This repository contains the analytical workflow used to process NaPi2b molecular dynamics trajectories and convert them into biologically interpretable graph representations. The project is centered on the hypothesis that tumor-like conditions can induce hidden local rewiring in extracellular regions and epitope-associated subdomains even when classical global MD descriptors show only limited differences.

The repository is currently organized around two main analytical stages:

- **Phase3A: preprocessing and feature extraction** — standardized extraction of residue-level, contact-level, and non-protein interaction data from molecular dynamics trajectories.
- **Phase3B: topological graph analysis** — construction and analysis of residue interaction networks, including centrality, community structure, regional epitope analysis, and temporal graph behavior.

## Biological motivation

NaPi2b (SLC34A2) is a membrane transporter of interest in cancer research, particularly in ovarian cancer, where it is considered a relevant biomarker and therapeutic target. Because tumor microenvironments differ from normal tissue in pH, membrane composition, and interaction context, the project investigates whether these conditions affect the structural and topological organization of extracellular NaPi2b regions that may be relevant for recognition and targeting.

## Research question

The main question addressed in this repository is:

> Can tumor-like conditions reshape the local residue interaction topology of NaPi2b in ways that are not fully captured by classical MD metrics, but become visible after transforming trajectories into residue interaction networks?

## Systems analyzed

Two condition-specific systems are currently processed through the same analytical logic:

| System | Description |
|---|---|
| `normal` | Reference condition used as the structural and topological baseline. |
| `tumor` | Tumor-like condition used to evaluate condition-dependent rewiring of the NaPi2b interaction network. |

Both conditions are processed through a common export and analysis pipeline, making downstream comparison reproducible and explicit.

## Pipeline structure

### Phase3A — preprocessing

Phase3A converts raw trajectory-derived information into a standardized set of exports for graph-based downstream analysis. This stage includes residue feature extraction, contact definition, integration of lipid and glycan context, and generation of machine-readable artifacts.

Key outputs from this stage include:

- residue tables for each condition,
- protein contact edge tables, including undirected variants,
- lipid, glycan, and non-protein node tables,
- non-protein contact edge files,
- heterograph exports for graph workflows,
- 3D coordinate mappings for structural visualization,
- per-frame contact edge parquet files for temporal analysis.

### Phase3B — graph analysis

Phase3B consumes the exported Phase3A artifacts and performs graph-based analysis of NaPi2b under each condition. This includes graph QC, consensus network generation, condition-wise centrality comparison, regional extracellular domain and epitope analysis, community detection, and temporal residue interaction network analysis.

The notebook also contains figure-generation logic for publication-style outputs, including regional contact views, community reshuffling summaries, and temporal graph metric panels.

## Repository goals

This repository is designed to serve several purposes at once:

- a reproducible scientific workspace for NaPi2b structural analysis,
- a transparent bridge between molecular dynamics and graph-based biological interpretation,
- a foundation for future pipeline modularization and packaging,
- a basis for downstream mutation analysis, ML experiments, and interactive tooling.

## Suggested repository layout

```text
./
├─ README.md
├─ .gitignore
├─ notebooks/
│  ├─ Phase3A_preprocessing.ipynb
│  └─ Phase3B_topological_analysis.ipynb
├─ docs/
│  ├─ project-overview.md
│  ├─ data-contract.md
│  ├─ repository-map.md
│  └─ figures/
│     ├─ figure_1.png
│     ├─ figure_2.png
│     ├─ figure_3.png
│     └─ figure_4.png
└─ data/
   ├─ README.md
   └─ example/
```

## Figures

- **Figure 1. QC overview.** Structural and interaction-level quality control of preprocessing outputs for normal and tumor conditions.
- **Figure 2. Regional extracellular topology.** Example of condition-specific graph analysis focused on extracellular or epitope-associated regions of NaPi2b.
- **Figure 3. Community reshuffling.** Condition-dependent reassignment of graph communities between normal and tumor-like NaPi2b networks.
- **Figure 4. Temporal RIN behavior.** Time-resolved graph metrics showing dynamic differences between condition-specific residue interaction networks.

## How to start

For a new reader, the recommended entry points are:

1. Read this `README.md` for the project scope and workflow.
2. Open `Phase3A_preprocessing_scientific_v5.6.ipynb` to understand the preprocessing logic and exported artifacts.
3. Open `Phase3B_Topological_Graph_Analysis_v6.ipynb` to see how the graph analysis is built on top of Phase3A outputs.
4. Review the curated figures in `docs/figures/` before diving into the full notebooks.

## Current status

The repository already reflects a fairly mature analysis logic in which preprocessing exports and graph analytics are explicitly connected. The next step toward external usability would be to formalize this structure into a cleaner repository layout with stable paths, figure folders, and short supporting documentation pages.

## Future work

- Stabilize the export manifest and folder conventions for reproducible reuse.
- Refactor reusable notebook logic into package-style helper modules.
- Extend condition comparison toward mutation-aware analysis, including disease-relevant variants.
- Connect the workflow to a more formalized pipeline and visualization interface: https://github.com/2Myaka2/MANIA_WANIA/

## Russian summary

Этот репозиторий посвящен анализу транспортера NaPi2b (SLC34A2) методами структурной биоинформатики, объединяющими молекулярную динамику, residue interaction networks и топологический анализ графов. Основная идея проекта состоит в том, что опухолеподобные условия могут вызывать локальную перестройку сетевой архитектуры белка, особенно во внеклеточных областях и эпитопных регионах, даже если классические глобальные MD-метрики показывают лишь умеренные различия.
