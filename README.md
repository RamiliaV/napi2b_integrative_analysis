# Integrative Structural-Dynamic Analysis of NaPi2b (SLC34A2)

Reproducible molecular-dynamics (MD), residue-interaction network (RIN), topological, and pilot spatiotemporal graph-neural-network (ST-GNN) analysis of the membrane phosphate transporter NaPi2b under normal- and tumor-like simulated microenvironments.

> **Scope of this repository.** The repository documents a computational pilot and provides reproducible analysis artifacts. It does not present a clinically validated classifier or establish causal effects of any individual microenvironmental factor.

## Overview

NaPi2b (SLC34A2) is a sodium-dependent phosphate transporter expressed in epithelial tissues and investigated as a molecular target in ovarian cancer. Its conformational ensemble is influenced by the membrane, ionic environment, and protonation conditions. Static structures and conventional MD descriptors alone do not fully capture how residue contacts reorganize over time.

This project represents each MD frame as a residue-interaction network and analyses the resulting dynamic graph sequence along two complementary tracks:

1. **Topology-first analysis** of dynamic RINs: centrality, communities, regional/epitope contact structure, and differential network properties.
2. **Pilot ST-GNN analysis**: graph-based conformation encoding, latent-space analysis, blocked temporal evaluation, and label-permutation control.

```text
Atomistic MD trajectories
          |
          v
MANIA preprocessing and dynamic RIN construction
          |
          +-----------------------------+
          |                             |
          v                             v
Topological analysis              Pilot ST-GNN analysis
centralities, communities,        graph encoder, temporal model,
regions, contacts                 latent-space and control analyses
          |                             |
          +-------------+---------------+
                        v
         Reproducible results, QC, manifests, and figures
```

## Study design

### Protein system

- **Protein:** NaPi2b / SLC34A2.
- **Starting model:** AlphaFold model AF-O95436-F1.
- **System context:** fully atomistic membrane-protein systems with glycans and disulfide bonds as specified in the MD protocol.
- **MD engine:** GROMACS; the broader protocol uses CHARMM36m-compatible parameters and constant-pH MD design elements.

### Simulated microenvironments

The simulations compare two *modelled* physicochemical contexts rather than clinical samples.

| Feature | Normal-like condition | Tumor-like condition |
|---|---:|---:|
| Class label | `normal` / 0 | `tumor` / 1 |
| Target pH | 7.4 | 6.8 |
| Membrane context | Healthy-like asymmetric lipid profile | Tumor-like asymmetric lipid profile |
| Temperature in production protocol | 310 K | 312 K |
| NaCl | 145 mM | 170 mM |
| MgCl2 | 1.0 mM | 0.65 mM |
| Extracellular ganglioside signature | GM1-containing outer leaflet | GM3-containing outer leaflet |

The condition definitions jointly modify pH, ion composition, and membrane composition. Therefore, downstream differences are interpreted as effects associated with the **integrated modelled microenvironment**, not as causal effects of a single lipid, ion, or pH change.

## Dynamic RIN construction

### Graph representation

Each selected MD frame is transformed into a residue-interaction network:

- **Protein nodes:** amino-acid residues of NaPi2b.
- **Optional non-protein nodes:** lipids, glycans, and ligands when present in the exported MANIA artifacts.
- **Edges:** physical interactions detected from the trajectory and represented as relation types in heterogeneous graphs.
- **Temporal information:** consecutive graph frames are assembled into fixed-length temporal windows for downstream analysis.

The preprocessing contract retains per-frame contacts, residue-level attributes, non-protein nodes, QC summaries, and explicit manifests. Periodic-boundary-condition-aware preprocessing is used for geometry-based contact calculations.

### Interaction types

The workflow supports the following relation classes, depending on the preprocessing export and analysis stage:

- backbone contacts;
- hydrogen bonds;
- van der Waals contacts;
- hydrophobic contacts;
- ionic contacts and salt bridges;
- aromatic and cation-pi interactions;
- disulfide bonds;
- protein-lipid, protein-glycan, glycan-anchor, and protein-ligand interactions.

### Principal preprocessing artifacts

| Artifact | Purpose |
|---|---|
| `contact_edges_perframe_*.parquet` | Per-frame dynamic contact edges for graph construction |
| `residue_table_*.csv` | Residue identifiers and structural features |
| `protein_contact_edges_undirected_*.csv` | Aggregate protein-protein contact statistics |
| `nonprotein_nodes_*.csv` | Lipid/glycan/ligand node definitions |
| `np_contact_edges_*.csv` | Protein-to-non-protein contacts |
| `*_manifest.json`, `dataset_manifest.json` | Provenance, configuration, and validation metadata |
| QC CSV/JSON files | Input integrity, node/edge coverage, and preprocessing checks |

## Topological analysis

The topology-first branch quantifies how the dynamic RIN is organised within each simulated condition and identifies regions whose network properties differ between conditions.

### Analyses

- Node centralities, including degree, betweenness, closeness, eigenvector centrality, and k-core-related features.
- Differential centrality statistics and false-discovery-rate-aware comparisons.
- Community structure in normal-like and tumor-like networks.
- Regional analyses of extracellular, epitope-associated, and inter-region contact patterns.
- Protein–non-protein contact summaries and differential contact profiles.
- Temporal RIN descriptors and convergence/divergence outputs.

### Selected output tables

| File | Description |
|---|---|
| `results/tables/centrality_delta.csv` | Condition-level differences in node centralities |
| `results/tables/community_normal.csv` | Community assignments in the normal-like condition |
| `results/tables/community_tumor.csv` | Community assignments in the tumor-like condition |
| `results/tables/temporal_rin_*.csv` | Temporal network descriptors, when available |
| `results/tables/statistics_mwu_fdr.csv` | Statistical comparisons with FDR correction, when available |

## Pilot ST-GNN analysis

### Aim

The ST-GNN pilot asks whether dynamic RINs derived from normal-like and tumor-like MD trajectories contain a label-associated signal that can be extracted by a graph-temporal model.

This is a **methodological pilot**. It demonstrates the technical integration of MANIA-generated dynamic graphs with machine learning; it does not provide an independently replicated estimate of biological separation between simulated conditions.

### Input representation

- Each frame is represented as a heterogeneous RIN.
- Node types include protein residues and, where available, lipid and glycan entities.
- Graph relations encode residue-residue and protein–non-protein interaction types.
- Consecutive frames are combined into temporal windows of **5 frames**.
- The pilot data use blocked temporal groups to reduce direct mixing of neighbouring windows between subsets.

### Architecture

#### Graph encoder

A heterogeneous graph encoder projects protein, lipid, and glycan node features into a shared hidden representation and applies two `HeteroConv` layers based on `SAGEConv` relations. Protein-node representations are aggregated with attention pooling.

- Frame hidden dimension: **128**
- Graph-convolution layers: **2**
- Dropout: **0.30**
- Pooling: attention pooling

#### Conformation autoencoder

`ConformationAutoencoderV4` learns compact frame-level conformation representations before temporal reconstruction.

```text
Heterogeneous RIN frame
        |
        v
Graph encoder (hidden dimension 128)
        |
        v
Projection: 128 -> 64 -> 24
        |
        v
Latent conformation representation
        |
        v
Two-layer LSTM encoder/decoder (hidden dimension 128)
```

- Latent dimension: **24**
- LSTM layers: **2**
- LSTM hidden dimension: **128**
- Reconstruction loss: mean squared error in latent space
- Anti-collapse term: latent-norm target = **1.0**; regularisation weight = **1e-3**

The anti-collapse penalty prevents a trivial solution in which all latent vectors approach zero.

#### Spatiotemporal classifier

`SpatioTemporalGNN` encodes every frame in a temporal window, integrates frame embeddings with a GRU, and predicts a normal-like or tumor-like label.

```text
RIN(t1), RIN(t2), ..., RIN(t5)
        |
        v
Graph encoder per frame
        |
        v
5 frame embeddings (128 dimensions)
        |
        v
Two-layer GRU (hidden dimension 128)
        |
        v
MLP classifier: 128 -> 64 -> 2
```

- Temporal window: **5 frames**
- GRU layers: **2**
- GRU hidden dimension: **128**
- Dropout: **0.30**
- Output classes: **2**

### Training settings

| Parameter | Value |
|---|---:|
| Optimiser | Adam |
| Learning rate | 1e-3 |
| Weight decay | 1e-4 |
| Maximum epochs | 100 |
| Early-stopping patience | 15 epochs |
| Classification threshold | 0.5 |
| Pseudo-random seeds | 42, 44, 7, 123, 2026 |

### Pilot validation design

The pilot uses blocked temporal evaluation. Temporal windows are grouped into non-overlapping time blocks before train/validation/test assignment. Two blocked split configurations and five random seeds yield **10 real-label ST-GNN runs**.

An explicit **label-permutation control** uses the same model architecture and training settings but disrupts the correspondence between data and normal/tumor labels.

> **Critical limitation:** temporal blocking reduces direct temporal leakage but does not create independent biological or MD replicates. Windows from a trajectory remain autocorrelated. The pilot must therefore not be interpreted as an independent-replica validation.

### Pilot results

| Analysis | ROC-AUC | F1 | MCC | Accuracy | Runs |
|---|---:|---:|---:|---:|---:|
| ST-GNN, original labels | 0.665 | 0.768 | 0.500 | 0.752 | 10 |
| ST-GNN, permutation baseline | 0.584 | 0.671 | 0.000 | 0.504 | 10 |

The original-label runs achieve a mean MCC of **0.50**, whereas the permutation baseline has MCC = **0.00**. This indicates that the pilot pipeline can extract a label-associated structure that is not retained after label permutation.

However, the individual fold-by-seed results are strongly variable and show seed-dependent bimodality: some training runs strongly separate the simulated contexts, while others remain near chance-level classification. Consequently, this result is interpreted as evidence of **technical feasibility and a hypothesis-generating signal**, not as confirmed biological discrimination.

### Latent-space baselines

Classical classifiers trained on the learned latent features show high internal performance in the pilot data. These values are retained as exploratory diagnostics of latent-space structure only.

| Model | ROC-AUC | F1 | MCC | Accuracy |
|---|---:|---:|---:|---:|
| Logistic Regression | 1.000 | 0.988 | 0.977 | 0.988 |
| Random Forest | 0.872 | 0.651 | 0.416 | 0.699 |
| Gradient Boosting | 0.901 | 0.733 | 0.595 | 0.779 |
| SVM with RBF kernel | 0.999 | 0.991 | 0.982 | 0.991 |

The nearly perfect internal metrics of Logistic Regression and SVM should **not** be interpreted as out-of-trajectory or biological generalisation. They may reflect genuine structure in the latent space, residual temporal-block structure, or both. Independent-replica validation is required.

### ST-GNN results and artifacts

| File | Description |
|---|---|
| `notebooks/ST_GNN_Pilot2.ipynb` | Pilot ST-GNN notebook, including preprocessing-artifact loading, graph construction, autoencoder, ST-GNN, baselines, and evaluation |
| `results/figures/ae_training_curves.png` | Autoencoder reconstruction-loss and latent-norm training curves |
| `results/figures/umap_latent_space.png` | UMAP projection of latent representations |
| `results/figures/stgnn_real_vs_permutation.png` | Comparison of real-label and permutation-control ST-GNN results |
| `results/tables/stgnn_results_real_per_fold_seed.csv` | Per-run metrics across blocked folds and pseudo-random seeds |
| `results/tables/stgnn_summary_bootstrap_ci.csv` | Bootstrap confidence intervals for ST-GNN and permutation results |
| `results/tables/final_comparison_table.csv` | Summary comparison of ST-GNN and latent-feature baseline models |
| `results/manifests/stgnn_pilot_blocked_temporal_manifest.json` | Pilot configuration, provenance, and validation metadata |

## Repository layout

```text
.
├── README.md
├── docs/                       # Protocols and supplementary documentation
├── notebooks/                  # Reproducible analysis notebooks
│   └── ST_GNN_Pilot2.ipynb
└── results/
    ├── figures/                # Publication/presentation figures
    │   ├── ae_training_curves.png
    │   ├── stgnn_real_vs_permutation.png
    │   └── umap_latent_space.png
    ├── manifests/              # Configuration and provenance files
    │   └── stgnn_pilot_blocked_temporal_manifest.json
    └── tables/                 # Machine-readable result tables
        ├── centrality_delta.csv
        ├── community_normal.csv
        ├── community_tumor.csv
        ├── final_comparison_table.csv
        ├── stgnn_results_real_per_fold_seed.csv
        └── stgnn_summary_bootstrap_ci.csv
```

## Main conclusions

1. The repository establishes a reproducible route from atomistic NaPi2b MD trajectories to dynamic residue-interaction networks and graph-based analysis artifacts.
2. The topology branch provides condition-specific centrality, community, and regional contact outputs for structural interpretation.
3. In a blocked temporal pilot, the ST-GNN result on original labels exceeds the permutation baseline (mean MCC 0.50 versus 0.00).
4. The ST-GNN result is seed-dependent and is not yet independently replicated; it should be treated as a technical and hypothesis-generating result.
5. Confirmatory analysis requires independent MD replicas and strict splitting by trajectory/run identifier rather than frames or temporal windows.

## Limitations

- Simulated normal-like and tumor-like conditions change several factors jointly, including pH, ions, and membrane composition.
- Current pilot windows are temporally autocorrelated within MD trajectories.
- The pilot blocked split is stricter than random frame splitting but is not a replacement for independent-replica validation.
- The duration and number of trajectories limit claims about long-timescale conformational sampling.
- Force-field choice, membrane construction, contact definitions, and graph featurisation may influence the observed network patterns.

## Next steps

The next validation stage will process independent 100-ns MD replicas separately and use strict **replica-aware evaluation**:

```text
Independent MD runs
        |
        v
Separate MANIA preprocessing per run_id
        |
        v
Dynamic RINs and temporal windows
        |
        v
Training and test sets separated by run_id
        |
        v
Confirmatory ST-GNN and topology interpretation
```

No frames or temporal windows from the same MD trajectory should appear in both training and test subsets.

## Authors and contact

- **R.A. Vlasenkova** — study design, computational workflow development, technical implementation, molecular-dynamics and graph analysis, interpretation, and writing.
- **R.G. Kiyamova** — conceptualisation of the biological NaPi2b/ovarian-cancer research context.
- **N.I. Akberova** — conceptualisation of the bioinformatics direction and scientific supervision.

Institute of Fundamental Medicine and Biology, Kazan Federal University, Kazan, Russia  
Contact: [r.mukhamadeeva@yandex.ru](mailto:r.mukhamadeeva@yandex.ru)

## Citation

If you use this repository, please cite the associated project materials and contact the authors for the current citation format.
