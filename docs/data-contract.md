# Data contract between Phase3A and Phase3B

## Why this document exists

The project uses a staged workflow in which preprocessing and topological analysis are separated into different notebooks. This document explains which artifacts are produced upstream and how they are used downstream.

## Upstream notebook

`Phase3A_preprocessing_scientific_v5.6.ipynb` is the main preprocessing notebook. Its role is to convert trajectory-derived structural and interaction data into standardized intermediate artifacts.

## Downstream notebook

`Phase3B_Topological_Graph_Analysis_v6.ipynb` is the main graph-analysis notebook. It consumes the Phase3A exports and uses them to build condition-specific residue interaction networks and comparative graph statistics.

## Core exported artifacts

| Artifact family | Typical purpose | Used in Phase3B for |
|---|---|---|
| `residue_table_*.csv` | Residue-level metadata and features. | Region mapping, residue annotation, node-level analysis. |
| `protein_contact_edges_*.csv` | Directed or detailed residue-residue contact definitions. | Edge construction, contact filtering, graph assembly. |
| `protein_contact_edges_undirected_*.csv` | Symmetrized contact tables for graph workflows. | Undirected graph construction and consensus edge analysis. |
| `lipid_nodes_*.csv` | Lipid-associated node definitions. | Protein-lipid context analysis. |
| `glycan_nodes_*.csv` | Glycan node definitions. | Protein-glycan and extracellular context interpretation. |
| `non_protein_nodes_*.csv` | Unified non-protein interaction nodes. | Extended heterogeneous graph interpretation. |
| `np_contact_edges_*.csv` | Protein to non-protein interaction edges. | Heterogeneous graph and environment-aware analysis. |
| `heterograph_*.pt` | Serialized heterogeneous graph objects. | Graph-based ML or structured graph workflows. |
| `heterograph_norm_*.pt` | Normalized graph representations. | Comparative graph workflows and reusable downstream processing. |
| `pos3d_*.json` | 3D node coordinates. | Structural graph plotting and spatial mapping of results. |
| `contact_edges_per_frame_*.parquet` | Time-resolved edge data. | Temporal RIN analysis and window-based graph metrics. |

## Logic of the interface

Phase3A acts as a standardization layer between raw trajectory analysis and graph-centric interpretation. Phase3B assumes that residue tables, contact edges, and optional heterogeneous context have already been cleaned and exported in a stable format. In practical terms, this means that Phase3B should not have to recompute low-level contact extraction if the Phase3A outputs are present and version-matched.

## Recommended future stabilization

To make the workflow easier to maintain, the exported files should eventually follow a fixed directory convention and a machine-readable manifest. A simple next step would be a manifest file that records condition name, frame count, residue count, file paths, and version tags for each export bundle.