# Bigraph & Hypergraph Tools for BioJazz Lab

**Maintained by:** BioJazz Lab, Department of Biology, Baylor University  
**Last updated:** 2026-11-23  
**Purpose:** Track software for **Milner's bigraphs** (bigraphical reactive systems, BRS) and **hypergraphs**. This covers modelling, rewriting, simulation and verification of nested, linked biological systems, plus higher-order interaction network analysis.

> **Scope.** In this folder, "bigraph" means Robin Milner's bigraphs. A bigraph has a **place graph**, a forest that says what is nested inside what (cell ⊃ cytoplasm ⊃ nucleus), and a **link graph**, a hypergraph that says what is connected to what (complexes, bonds, channels). Reaction rules (redex → reactum) rewrite the bigraph, and can be stochastic. A link graph is a hypergraph, which is why the two topics share this folder.
>
> Several unrelated tools also use the word "bigraph" for bipartite graphs or genome graphs. They are not listed here, except the one noted under [Name Collisions](#name-collisions).

---

## Table of Contents

1. [Milner Bigraph Engines & Toolkits](#milner-bigraph-engines--toolkits)
2. [Bigraphs for Biological Simulation (Vivarium)](#bigraphs-for-biological-simulation-vivarium)
3. [Rust Tools for Bigraph Research](#rust-tools-for-bigraph-research)
4. [Related Compositional / Categorical Modelling](#related-compositional--categorical-modelling)
5. [Hypergraph Analysis Libraries](#hypergraph-analysis-libraries)
6. [Hypergraph Learning (GNNs & Topological Deep Learning)](#hypergraph-learning-gnns--topological-deep-learning)
7. [Hypergraph Partitioning](#hypergraph-partitioning)
8. [Data Formats & Datasets](#data-formats--datasets)
9. [Biology-Specific Hypergraph Applications](#biology-specific-hypergraph-applications)
10. [Legacy Bigraph Tools](#legacy-bigraph-tools)
11. [Name Collisions](#name-collisions)

---

## Milner Bigraph Engines & Toolkits

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **BigraphER** | Bigraph Evaluator & Rewriting (OCaml) from the University of Glasgow: bigraphs and bigraphs with sharing, SAT-based matching, probabilistic and stochastic rules, rule priorities, simulation, full state-space exploration, PRISM model-checker export, predicate checking | [Website](https://uog-bigraph.bitbucket.io/) / [Source](https://bitbucket.org/uog-bigraph/bigraph-tools) / [Docker](https://hub.docker.com/r/mseve/bigrapher) | The reference BRS engine; v1.9.3 binary or `docker pull mseve/bigrapher`. Stochastic rules + PRISM export suit biochemical signalling models; [VSCode extension](https://github.com/seelx3/vscode-bigrapher) |
| **Bigraph Framework** | Java framework for building, simulating and analysing BRS: Ecore metamodel, rule-based rewriting, BFS/DFS simulation and model checking, export to DOT/PNG, interoperability with BigraphER/jLibBig/BigMC | [GitHub](https://github.com/bigraph-toolkit-suite/bigraphs.bigraph-framework) / [Docs](https://bigraphs.org/software/bigraph-framework/docs/) / Maven `org.bigraphs.framework` | Apache-2.0; actively developed (Bigraph Toolkit Suite, FZ Jülich / TU Dresden); [project skeleton](https://github.com/bigraph-toolkit-suite/Project-Skeleton-for-Bigraph-Framework) and [exercises](https://github.com/bigraph-toolkit-suite/bigraphs.bigraph-framework-exercises) |
| **jLibBig** | Java library for bigraphical reactive systems, including directed bigraphs; matching posed as a constraint-satisfaction problem | [GitHub](https://github.com/bigraphs/jlibbig) / [Docs](https://bigraphs.github.io/jlibbig/) / [Project page](https://marcoperessotti.com/software/jlibbig) | LGPL-2.1; matching engine used by Bigraph Framework |
| **BDSL** | Domain-specific language for specifying and simulating bigraphs; interpreter and language server | [Interpreter](https://github.com/bigraph-toolkit-suite/bigraphs.bdsl-interpreter-parent) / [Grammar](https://github.com/bigraph-toolkit-suite/bigraphs.bdsl-grammar) | Apache-2.0; Bigraph Toolkit Suite; text-based alternative to the Java API |
| **BiGGer** | Translates bigraph models and rules into GrGen.NET files so GrGen's graph-rewriting engine can run them | [GitHub](https://github.com/bigraph-toolkit-suite/bigraphs.grgen-bigraphs) | Apache-2.0; useful when large models need faster rewriting |
| **Bigellor** | Web-based visual editor for bigraphs | [GitHub](https://github.com/bigraph-toolkit-suite/bigraphs.ide.bigellor) | Apache-2.0; for drawing models; does no rewriting |
| **bigraph (Python)** | Python implementation of Milner's bigraph formalism | [GitHub](https://github.com/prismofeverything/bigraph) / [PyPI](https://pypi.org/project/bigraph) | By Ryan Spangler (Vivarium author); small and experimental; last updated 2023 |

---

## Bigraphs for Biological Simulation (Vivarium)

Vivarium 2.0 arranges simulation state as a bigraph. Nested stores form the place graph, and the wires connecting processes to stores form the link graph. Base `process-bigraph` composes processes but does not do BRS rewriting. `viva-reactive-system` adds full Milner-style rewriting with redex → reactum rules on top.

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **viva-reactive-system** | process-bigraph Process that fires Milner-style redex → reactum rules in deterministic, stochastic or Gillespie SSA mode; worked MAPK signalling example on a nested `Cell ⊃ Cytoplasm ⊃ {Nucleus, ERLumen}` bigraph | [GitHub](https://github.com/vivarium-collective/viva-reactive-system) / [Workbench](https://vivarium-collective.github.io/viva-reactive-system/) | Apache-2.0; 2026, early stage. Closest match to biological BRS modelling in Python; can be coupled with other Vivarium processes (e.g. FBA, ODE) |
| **process-bigraph (Vivarium 2.0)** | Compositional simulation interfaces: processes wired into a nested bigraph of stores for multi-scale, multi-algorithm models | [GitHub](https://github.com/vivarium-collective/process-bigraph) | Apache-2.0; fits the lab's multi-scale eco-evolutionary and virtual-cell work |
| **bigraph-schema** | Compositional type system under process-bigraph (types, defaults, merge/apply semantics; JSON contract) | [GitHub](https://github.com/vivarium-collective/bigraph-schema) | Apache-2.0 |
| **bigraph-viz** | Draws bigraph-schema / process-bigraph composites as place + link diagrams | [GitHub](https://github.com/vivarium-collective/bigraph-viz) | Apache-2.0 |
| **vivarium-workbench** | Interactive dashboard for running, inspecting and reporting on process-bigraph workspaces | [GitHub](https://github.com/vivarium-collective/vivarium-workbench) | Apache-2.0; used by viva-reactive-system |
| **viva-core** | Rust engine for bigraph-schema + process-bigraph; Python ↔ Rust interchangeable | [GitHub](https://github.com/vivarium-collective/viva-core) | Apache-2.0; 2026, early stage; details under [Rust Tools](#rust-tools-for-bigraph-research) |
| **vivarium-core** | Vivarium 1.0 engine for composite multi-scale simulation | [GitHub](https://github.com/vivarium-collective/vivarium-core) | Apache-2.0; predecessor of process-bigraph; many existing models still use it |

---

## Rust Tools for Bigraph Research

> **State of the ecosystem (checked 2026-11-23).** No mature Rust library implements Milner's bigraphical reactive systems (place/link graphs, matching, reaction rules). Searches of crates.io ("bigraph", "bigraphs", "milner") and GitHub (Rust repos matching "bigraph" or "bigraphical") found only:
> - **viva-core**, a Rust engine for Vivarium's bigraph-schema / process-bigraph, and
> - the genome-graph `bigraph` crate, which is a different concept (see [Name Collisions](#name-collisions)).
>
> Everything else below is a building block for writing a Rust BRS engine, following the design of BigraphER: place forest + link hypergraph, SAT-based matching, stochastic rule firing.

### Bigraph engines

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **viva-core** | Rust reimplementation of bigraph-schema + process-bigraph, byte-compatible with the JSON contract. Cargo workspace (`bigraph-schema-rs`, `process-bigraph-rs`, PyO3 binding crate), with `rayon` threading and MPI for ensembles | [GitHub](https://github.com/vivarium-collective/viva-core) | Apache-2.0; ~160× faster than the Python engine in a prototype benchmark. Runs Python processes on the Rust engine and the reverse. Milestone 1 (engine MVP) in progress. Implements Vivarium's composition model, **not** BRS rewriting |

### Link-graph / hypergraph data structures

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **yamafaktory/hypergraph** | Directed hypergraph data structure; hyperedges join any number of vertices | [GitHub](https://github.com/yamafaktory/hypergraph) / [Docs](https://docs.rs/hypergraph) | MIT; candidate representation for a bigraph's link graph |
| **open-hypergraphs** | Open hypergraphs (hypergraphs with interfaces) with composition, from category theory | [GitHub](https://github.com/hellas-ai/open-hypergraphs) | Apache-2.0; open interfaces resemble a bigraph's inner/outer names, so it is useful for composition (⊗, ∘) |
| **Dachshund** | Graph mining over typed hypergraphs | [GitHub](https://github.com/facebookresearch/dachshund) | Meta; analysis rather than rewriting |

### Building blocks for matching, rewriting and simulation

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **petgraph** | Standard Rust graph library; includes VF2 subgraph isomorphism (`algo::subgraph_isomorphisms_iter`) | [GitHub](https://github.com/petgraph/petgraph) / [Docs](https://docs.rs/petgraph) | Apache-2.0/MIT; place-graph forest + simple redex matching prototypes |
| **RustSAT** | SAT-solving toolkit with encodings and interfaces to CaDiCaL, Kissat, Glucose and MiniSat | [GitHub](https://github.com/chrjabs/rustsat) / [Docs](https://docs.rs/rustsat) | MIT; BigraphER does bigraph matching by SAT encoding, and RustSAT is the natural way to do the same in Rust |
| **splr** | Pure-Rust CDCL SAT solver (Kissat-inspired) | [GitHub](https://github.com/shnarazk/splr) | No C dependency; alternative backend to RustSAT's bindings |
| **rebop** | Fast Gillespie stochastic simulator for chemical reaction networks; Rust macro DSL + Python package | [GitHub](https://github.com/Armavica/rebop) / [crates.io](https://crates.io/crates/rebop) / [PyPI](https://pypi.org/project/rebop/) | MIT; reference for the SSA loop when firing stochastic bigraph rules (as in viva-reactive-system's Gillespie mode) |
| **PyO3** | Rust ↔ Python bindings (with maturin for wheels) | [GitHub](https://github.com/PyO3/pyo3) | Apache-2.0/MIT; what viva-core uses to expose a Rust engine to the Python Vivarium stack |

---

## Related Compositional / Categorical Modelling

These are not bigraphs, but they come from the same line of work on composing open systems and wiring diagrams. They are useful for comparison or for interoperability.

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **Catlab.jl** | Applied category theory in Julia: wiring diagrams, attributed C-sets (ACSets), composition of open systems | [GitHub](https://github.com/AlgebraicJulia/Catlab.jl) / [Website](https://www.algebraicjulia.org) | MIT; foundation of the AlgebraicJulia ecosystem |
| **AlgebraicRewriting.jl** | Algebraic graph rewriting (DPO, SPO, SqPO) on ACSets, built on Catlab | [GitHub](https://github.com/AlgebraicJulia/AlgebraicRewriting.jl) | MIT; a general rewriting framework comparable to BRS reaction rules |
| **AlgebraicPetri.jl** | Compositional Petri-net / reaction-network models built on Catlab | [GitHub](https://github.com/AlgebraicJulia/AlgebraicPetri.jl) / [Docs](https://algebraicjulia.github.io/AlgebraicPetri.jl/) | MIT; composable biochemical reaction networks |
| **open-hypergraphs** | Data structure for open hypergraphs (string diagrams) with composition | [GitHub](https://github.com/hellas-ai/open-hypergraphs) | Apache-2.0; Rust; categorical take on hypergraph composition |

---

## Hypergraph Analysis Libraries

Rust hypergraph crates are listed under [Rust Tools](#link-graph--hypergraph-data-structures).

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **nx-backbone** | ~110 backbone-extraction functions on NetworkX for weighted, bipartite, temporal and **hypergraph** networks: MDL hypergraph backbones, statistically validated hypergraphs/cores, s-components, maximal hyperedges | [GitHub](https://github.com/brianckeegan/nx-backbone) / [Docs](https://www.brianckeegan.com/nx-backbone/) / `pip install nx-backbone[full]` | BSD-3; converts to/from XGI, HyperNetX, HypergraphX and HIF; `describe_backbone` writes methods text with citations |
| **HyperNetX (HNX)** | PNNL Python library for hypergraph analysis and visualization: s-paths, s-centrality, homology, clustering, drawing | [GitHub](https://github.com/pnnl/HyperNetX) / [Docs](https://hypernetx.readthedocs.io) | Most-used Python hypergraph library; [hypernetx-widget](https://github.com/pnnl/hypernetx-widget) for interactive Jupyter views |
| **XGI (CompleX Group Interactions)** | Python package for higher-order networks: hypergraphs, simplicial complexes, directed hypergraphs; statistics, generators, dynamics, drawing | [GitHub](https://github.com/xgi-org/xgi) / [Docs](https://xgi.readthedocs.io) | Directed hypergraphs fit reaction networks (substrates → products) |
| **HypergraphX (HGX)** | Python library for higher-order network analysis: motifs, communities, filtering, temporal and multiplex hypergraphs | [GitHub](https://github.com/HGX-Team/hypergraphx) / [Docs](https://hgx-team.github.io/hypergraphx/) | Strong on statistical inference and hypergraph motifs |
| **TopoNetX** | Computing on topological domains: simplicial, cell, path and combinatorial complexes, colored hypergraphs | [GitHub](https://github.com/pyt-team/TopoNetX) | MIT; data-structure layer for TopoModelX |
| **SimpleHypergraphs.jl** | Julia hypergraph package: incidence-matrix representation, community detection, modularity, visualization | [GitHub](https://github.com/pszufe/SimpleHypergraphs.jl) | MIT |

---

## Hypergraph Learning (GNNs & Topological Deep Learning)

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **DHG (DeepHypergraph)** | PyTorch library for graph and hypergraph learning: HGNN, HGNN+, HyperGCN, UniGNN, structure generation, visualization | [GitHub](https://github.com/iMoonLab/DeepHypergraph) / [Docs](https://deephypergraph.readthedocs.io) | Apache-2.0; most complete hypergraph-GNN library |
| **TopoModelX** | Topological deep learning: message passing on hypergraphs, simplicial, cell and combinatorial complexes | [GitHub](https://github.com/pyt-team/TopoModelX) / [Docs](https://pyt-team.github.io/topomodelx/) | MIT; pairs with TopoNetX |
| **PyTorch Geometric** | General GNN library; includes `HypergraphConv` | [GitHub](https://github.com/pyg-team/pytorch_geometric) | Use when hypergraph layers sit inside a larger PyG model |
| **Hyper-SAGNN** | Self-attention GNN for hyperedge prediction on variable-size, heterogeneous hypergraphs | [GitHub](https://github.com/ma-compbio/Hyper-SAGNN) | Ma lab (CMU); applied to multi-way biological interactions |

---

## Hypergraph Partitioning

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **Mt-KaHyPar** | Multi-threaded multilevel graph and hypergraph partitioner; Python bindings | [GitHub](https://github.com/kahypar/mt-kahypar) | MIT; partitions very large hypergraphs quickly |
| **KaHyPar** | Karlsruhe Hypergraph Partitioning: high-quality k-way and recursive bisection | [GitHub](https://github.com/kahypar/kahypar) / [Website](http://www.kahypar.org) | GPL-3.0; sequential, best solution quality |

---

## Data Formats & Datasets

| Resource | Description | Access | Notes |
|----------|-------------|--------|-------|
| **HIF (Hypergraph Interchange Format)** | JSON schema for exchanging hypergraphs between libraries | [GitHub](https://github.com/HIF-org/HIF-standard) | MIT; supported by HyperNetX, XGI, HypergraphX and nx-backbone |
| **HIF-datasets** | Hypergraph datasets packaged in HIF | [GitHub](https://github.com/Jpickard1/HIF-datasets) | Ready-made benchmarks |
| **xgi-data** | Standardized higher-order datasets with datasheets | [GitHub](https://github.com/xgi-org/xgi-data) | Loadable via `xgi.load_xgi_data()` |

---

## Biology-Specific Hypergraph Applications

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **CHESHIRE** | Hypergraph link prediction to gap-fill genome-scale metabolic models (reactions as hyperedges over metabolites) | [GitHub](https://github.com/canc1993/cheshire-gapfilling) | MIT; Nature Communications 2023; relevant to the lab's GEM work |

---

## Legacy Bigraph Tools

These are no longer maintained. They are listed for reading older papers and models.

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| ~~**BigMC**~~ | Explicit-state model checker for BRS | [GitHub](https://github.com/bigmc/bigmc) | GPL-3.0; last update 2014; superseded by BigraphER / Bigraph Framework |
| ~~**Big Red**~~ | Eclipse-based graphical editor for bigraphs and reaction rules | [GitHub](https://github.com/ale-f/big-red) / [core model](https://github.com/bigraph-toolkit-suite/bigraphs.bigred-core) | Last update 2013; superseded by Bigellor |
| ~~**BPL Tool**~~ | Standard ML tool for binding bigraphs (ITU Copenhagen BPL project) | [GitHub mirror](https://github.com/standardml/bpltool) | Last update 2011; historical interest |

---

## Name Collisions

| Tool | Description | Access | Notes |
|------|-------------|--------|-------|
| **bigraph-rs** | Rust crate for genome-graph "bigraphs": every node and edge has a unique **mirror** (e.g. reverse complement). **Not** Milner's bigraphs | [GitHub](https://github.com/sebschmi/bigraph-rs) / [crates.io](https://crates.io/crates/bigraph) | Kept because it is useful for de Bruijn / assembly graphs (e.g. phage genome assembly), but it has no place or link graph and no rewriting |
| **genome-graph** | Companion crate to bigraph-rs for genome graphs built on its mirrored bigraphs | [crates.io](https://crates.io/crates/genome-graph) / [Docs](https://docs.rs/genome-graph) | Also **not** Milner bigraphs |

---

## Contributing & Conventions

- Follow the conventions in the [main README](../README.md#contributing--conventions): bold tool name, short description, access link, notes on lab use cases.
- Only add a tool under the bigraph sections if it implements Milner's bigraphs (place + link graph, BRS). Put lookalike names under [Name Collisions](#name-collisions).
- Update the `Last updated` date at the top when making changes.
