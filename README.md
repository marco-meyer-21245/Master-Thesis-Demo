<div align="center">

# Modeling the Economic Impact of Trade Disruptions

### A global multimodal transport and trade simulation

**Master's Thesis · Economics · University of Zurich**

[![Status](https://img.shields.io/badge/status-active%20development-1f8a70?style=for-the-badge)](#-latest-update)
[![Focus](https://img.shields.io/badge/focus-trade%20%7C%20networks%20%7C%20risk-111827?style=for-the-badge)](#-what-the-model-does)

**What happens to international trade when critical transport infrastructure becomes unavailable or dramatically more expensive?**

</div>

---

## 🎬 Video demo

<div align="center">

[![Watch the 54-second demo](assets/demo/trailer-thumbnail.png)](assets/demo/trailer.mp4)

**▶ Watch the 54-second trailer**

*Saudi Arabia → Switzerland oil trade · multimodal routing · Hormuz / Ukraine disruption · re-routing and modal substitution*

</div>

> **Demo in one sentence:** choose a country pair and product, compute a portfolio of multimodal trade routes and average transport costs, shock selected infrastructure links, then let the network re-route and substitute between modes.

---

## 🆕 Latest update

### MARNET upgrade — rebuilding the maritime layer
**October 2026 · Work in progress**

The current development focus is a major upgrade of the maritime network. The objective is to make global sea routing more physically credible around coastlines, narrow straits and chokepoints while keeping it fully connected to ports and the rest of the multimodal graph.

The upgraded pipeline is being built around:

- **high-resolution maritime geometry** using GEBCO and OpenStreetMap water / land information;
- an **adaptive water-cell graph** rather than a coarse, uniform ocean representation;
- explicit handling of **ports, maritime gateways and strategic chokepoints**;
- compatibility with the same routing and counterfactual-shock framework used by the rest of the model;
- targeted validation around difficult passages before replacing the production maritime layer.

<div align="center">
  <img src="assets/latest/marnet-01.png" width="32%" alt="MARNET upgrade — overview">
  <img src="assets/latest/marnet-02.png" width="32%" alt="MARNET upgrade — maritime detail">
  <img src="assets/latest/marnet-03.png" width="32%" alt="MARNET upgrade — chokepoint detail">
</div>

<p align="center"><sub>Latest development snapshots — replace these files to update the gallery without changing the README.</sub></p>

---

## 🌍 What the model does

This thesis connects **economic trade flows** to a **physical global transport network**.

| | |
|---|---|
| **1 · Represent economies geographically** | National production and demand are mapped to population centres rather than a single arbitrary country coordinate. |
| **2 · Build the transport network** | Road, rail, air and maritime infrastructure are combined into a global multimodal graph. |
| **3 · Infer transport costs** | Product- and mode-specific transport costs are estimated from observed expenditure, shipment weight and transport work. |
| **4 · Compute route portfolios** | The model searches for cost-efficient routes while reproducing plausible modal shares across road, rail, maritime and air transport. |
| **5 · Apply disruptions** | Infrastructure links can be made more expensive or effectively unavailable. |
| **6 · Re-route endogenously** | The network recomputes routes, allows modal substitution and produces a new average generalized transport cost. |
| **7 · Feed the economic model** | The resulting trade-cost shock can be passed into the international trade model to study wider economic propagation. |

---

## ⚡ How it works

```mermaid
flowchart LR
    A["Trade flow<br/>Exporter · Importer · Product"] --> B["Population-centre pairs"]
    B --> C["Predicted modal shares"]
    C --> D["Multimodal route portfolio"]
    D --> E["Average generalized<br/>transport cost"]
    E --> F["Network disruption"]
    F --> G["Re-routing"]
    G --> H["Modal substitution"]
    H --> I["New trade cost"]
    I --> J["Economic impact"]

    classDef input fill:#0f172a,stroke:#38bdf8,color:#ffffff;
    classDef model fill:#111827,stroke:#94a3b8,color:#ffffff;
    classDef shock fill:#3f1720,stroke:#fb7185,color:#ffffff;
    classDef output fill:#10251e,stroke:#34d399,color:#ffffff;

    class A input;
    class B,C,D,E model;
    class F shock;
    class G,H model;
    class I,J output;
```

The routing problem is solved separately for each origin–destination–product flow. National trade is distributed across population-centre pairs, the model predicts modal-work shares, builds a portfolio of alternative routes, and allocates trade across that portfolio.

The result is not simply **one shortest path**. It is a transport solution with:

**modal shares · alternative routes · route weights · generalized costs · distance · time**

---

## 🛢️ Demo scenario: Saudi Arabia → Switzerland oil trade

The trailer uses one deliberately simple scenario to show the full workflow.

### 1 · Simulate trade
The user selects:

**Exporter:** Saudi Arabia  
**Importer:** Switzerland  
**Product:** Oil

The model then constructs origin–destination pairs between the countries' population centres and computes the relevant multimodal transport solution.

### 2 · Multimodal trade routes
For each population-centre pair, the model:

- predicts the expected **modal-work shares**;
- finds the generalized-cost shortest path;
- searches for **alternative mode-oriented routes**;
- combines them into a **route portfolio**;
- allocates transport across routes while remaining as close as possible to the predicted modal composition;
- reports the resulting **average generalized transport cost**.

### 3 · Counterfactual disruption
The demo then applies large cost increases to selected infrastructure links associated with the **Hormuz disruption** and routes affected by the **Ukraine war**.

The intervention changes the generalized cost of the selected edges. The physical graph itself remains the same, but the economic attractiveness of traversing the affected infrastructure changes sharply.

### 4 · Re-routing and modal substitution
The model recomputes the route portfolio under the shocked network.

Two adjustments can occur simultaneously:

- **within-mode re-routing** — e.g. maritime traffic uses a different maritime corridor;
- **between-mode substitution** — the relative use of road, rail, maritime and air changes when their relative costs change.

The output is a new set of routes, modal shares and average trade costs that can be compared directly with the baseline.

---

## 🕸️ The global transport graph

The transportation system is represented as a directed graph:

\[
G = (V,E)
\]

where nodes \(V\) represent geographic locations and edges \(E\) represent physical transport connections.

Two node classes are particularly important:

- **critical nodes** — population centres, ports, airports and other economically important gateways;
- **junction nodes** — locations where the physical network branches or different network segments meet.

### Population centres
Economic data are reported at country level, while transportation happens at geographic locations.

To bridge the two, populated cells are grouped into a small number of representative centres. Each centre carries a share of national population and therefore a share of the country's trade flow.

This allows a country such as Switzerland or Saudi Arabia to be represented by several economically meaningful geographic origins / destinations instead of one centroid.

### Road and rail
OpenStreetMap roads and railway tracks are transformed into grid-based networks.

The raw geographic network is then simplified by:

- identifying junctions and critical nodes;
- collapsing degree-2 chains while preserving total distance;
- pruning dead ends that do not connect economically relevant nodes;
- removing dangling components that remain disconnected from critical infrastructure.

This produces a much smaller graph while retaining the connectivity needed for routing.

### Air
Freight airports form the gateway nodes of the air layer. They connect national land networks to international air transport and allow air transport to compete with other modes when economically plausible.

### Maritime
Ports connect the land network to the maritime layer. Strategic chokepoints are represented explicitly so that disruptions can be applied to economically meaningful infrastructure rather than arbitrary map coordinates.

The maritime layer is currently the main area of active development — see the [latest MARNET upgrade](#-latest-update).

---

## 💰 From observed transport data to network costs

The model distinguishes between **monetary transport cost** and the **economic cost of time**.

For mode \(m\), product \(g\) and distance \(D\), the line-haul transport cost is approximated by:

\[
\hat{M}_{mg}(D) = \hat{a}_{mg} + 	ilde{b}_{mg}D
\]

The parameters are inferred from observed transport expenditure, shipment weight and transport work.

Product-specific estimates are useful because moving crude oil, refrigerated food and high-value electronics does not impose the same transport requirements. When product-level data are sparse, the estimates are shrunk toward broader product / mode estimates rather than relying on unstable coefficients.

The edge-level generalized cost additionally values time in transit:

\[
G_{e,g} = M_{e,g} + H_{e,g}
\]

where \(M_{e,g}\) is monetary transport cost and \(H_{e,g}\) captures the product-dependent cost of transport time and delay.

<details>
<summary><strong>Why generalized cost matters</strong></summary>

A route can be cheap in direct monetary terms but economically unattractive because it is slow, unreliable or requires costly handling.

Generalized cost gives the routing algorithm a common objective that can combine:

- line-haul transport cost;
- fixed access / terminal components;
- distance;
- travel time;
- additional delay;
- product value density;
- product-specific sensitivity to time.

This allows two geographically similar routes to have very different economic costs.

</details>

---

## 🚚 Modal shares and route portfolios

The model does not assume that one transport mode carries the entire flow.

For each shipment, it first predicts the relative attractiveness of road, rail, maritime and air transport from shipment characteristics such as:

- quantity;
- value density;
- distance;
- infrastructure accessibility;
- cargo category;
- relevant interaction effects.

A softmax model converts these scores into predicted modal-work shares.

The routing stage then builds a set of feasible alternative routes:

\[
\mathcal{R}_{odg} = \{r_1,r_2,\ldots,r_R\}
\]

and chooses route weights that first minimize the mismatch between the **predicted modal shares** and the **modal composition actually achievable on the physical network**.

Among equally good modal matches, it selects the lower-cost portfolio.

That distinction is important:

> **The statistical model says what modal mix is plausible; the transport graph determines what is physically achievable.**

---

## 💥 Modeling disruptions

A disruption is introduced as an exogenous increase in the generalized cost of selected network edges:

\[
G_{e,g}^{1} = G_{e,g}^{0} + \Delta G_{e,g}
\]

The routing problem is then solved again on the shocked cost structure.

For each mode, the model measures how its effective cost changes and updates its modal attractiveness. The final counterfactual therefore captures both:

**route substitution within a mode**  
and  
**substitution between transport modes**

This makes it possible to study scenarios such as:

- maritime chokepoint closures;
- war-affected corridors;
- port or airport disruptions;
- border frictions;
- infrastructure outages;
- mode-specific cost shocks.

---

## 📈 Connecting transport shocks to the economic model

The transport model produces changes in bilateral, product-specific trade costs.

These shocks are the bridge between the physical transport network and the economic model.

The broader thesis uses an international trade framework with sectoral linkages to study how higher trade costs can propagate through:

**import prices → sourcing decisions → intermediate inputs → production costs → expenditure → trade flows**

The objective is therefore not only to answer:

> *“Where will the cargo go instead?”*

but ultimately:

> **“What are the economic consequences when global trade has to go another way?”**

---

## 📊 Data and scale

The project combines economic, transport and geographic datasets at very different scales.

| Source / family | Role in the model |
|---|---|
| **WIOD / WIOT** | International production, intermediate-input and final-demand structure |
| **UNCTAD transport-cost data** | Transport expenditure, shipment weight, transport work and modal information |
| **OpenStreetMap** | Road, rail and supporting geographic infrastructure |
| **GHSL** | Population distribution used to construct geographic population centres |
| **GEBCO** | Maritime geometry / bathymetric information used in the maritime-network upgrade |
| **Ports / airports / chokepoints datasets** | Critical multimodal gateway nodes |
| **Maritime reference data** | Connectivity and validation of global sea routing |

The cost-inference pipeline is designed for very large transport datasets — the working UNCTAD extraction contains approximately **863 million raw observations across 2016–2019** — so the estimation process is built around chunking and sufficient statistics rather than loading the full dataset into memory.

---

## 🧠 What this project combines

<div align="center">

| Economics | Quantitative methods | Engineering |
|---|---|---|
| International trade | Constrained optimization | Large-scale data pipelines |
| Input-output linkages | Weighted regression | Graph construction |
| Trade costs | Multinomial / softmax models | Geospatial processing |
| Counterfactual analysis | Numerical simulation | Multimodal routing |
| Modal substitution | Hierarchical shrinkage | Interactive visualization |

</div>

### Core stack

`Python` · `NumPy` · `SciPy` · `graph-tool` · `Kuzu` · `GeoPandas` · `Rasterio` · `OpenStreetMap` · `PMTiles` · `MapLibre`

---

## 🔍 Technical core

<details>
<summary><strong>1 · Population-centre construction</strong></summary>

Countries are represented by several geographic population centres rather than one centroid.

Population cells are assigned to population-weighted centres on the sphere, producing population basins. Each final centre receives a national population share that is later used to distribute bilateral trade across origin–destination centre pairs.

This preserves country-level consistency while introducing realistic geography into the transport problem.

</details>

<details>
<summary><strong>2 · Network simplification</strong></summary>

The raw road and rail networks are much denser than required for global routing.

After rasterizing the relevant infrastructure, the model keeps critical nodes and graph junctions, collapses degree-2 chains and prunes structures that do not contribute to connectivity between critical locations.

Bridge detection is used when identifying dangling network components that can be removed without destroying economically relevant connectivity.

</details>

<details>
<summary><strong>3 · Cost inference</strong></summary>

Observed expenditure per tonne and implied shipment distance are reconstructed from transport expenditure, weight and transport work.

Mode / product cost functions are estimated with weighted regression. Product-specific estimates are reliability-adjusted toward broader parent estimates when data are sparse or noisy.

The resulting parameters are translated into edge-level network weights.

</details>

<details>
<summary><strong>4 · Route portfolio optimization</strong></summary>

For every origin–destination–product pair:

1. predict modal-work shares;
2. compute the generalized-cost shortest path;
3. search for additional mode-oriented alternatives;
4. calculate each route's modal composition;
5. allocate transport across routes to minimize modal-share mismatch;
6. among equally good modal matches, minimize average generalized route cost.

The solution therefore respects both observed modal patterns and physical network constraints.

</details>

<details>
<summary><strong>5 · Shocked routing</strong></summary>

Selected edges receive an exogenous generalized-cost increase.

The model rebuilds the route portfolio, measures relative modal cost changes, updates modal shares using estimated substitution parameters and solves the allocation problem again.

Baseline and counterfactual outputs can then be compared directly.

</details>

---

## 🚧 Project status

This repository is a **research demo** of an active Master's thesis.

The emphasis is on making the model, assumptions and counterfactual mechanism understandable without requiring the full research environment or hundreds of gigabytes of source data.

**Current focus:** maritime-network upgrade and validation.

---

## 👤 Author

**Marco Meyer**  
Master's in Economics · University of Zurich  
Minor in Quantitative Finance

[LinkedIn](https://www.linkedin.com/in/meyeramarco/)

---

<div align="center">

### From physical disruption to economic impact.

**Transport networks · Trade costs · Counterfactuals · Global economics**

</div>
