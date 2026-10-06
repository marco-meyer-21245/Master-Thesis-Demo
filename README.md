<div align="center">

# Modeling the Economic Impact of Trade Disruptions

### A global multimodal transport and trade simulation

**Master's Thesis · Economics · University of Zurich**

[![Status](https://img.shields.io/badge/status-active%20development-1f8a70?style=flat-square)](#-latest-update)
[![Demo](https://img.shields.io/badge/demo-54%20sec-2563eb?style=flat-square)](#-video-demo)
[![Focus](https://img.shields.io/badge/focus-trade%20%7C%20networks%20%7C%20risk-111827?style=flat-square)](#-project-overview)

**How do disruptions to transport infrastructure change trade routes, transport costs, and ultimately economic outcomes?**

</div>

---

## Project overview

This thesis links **international trade flows** to a **physical global transport network**. Instead of treating bilateral trade costs as an abstract number, the model asks how goods can actually move between countries through road, rail, air and maritime infrastructure — and what happens when part of that network is disrupted.

A selected exporter, importer and product are translated into geographic origin–destination pairs. The model predicts plausible modal shares, constructs a portfolio of multimodal routes, computes generalized transport costs, applies infrastructure shocks, and then recomputes routes and modal choices.

**Core stack:** `Python` · `NumPy` · `SciPy` · `graph-tool` · `Kuzu` · `GeoPandas` · `Rasterio` · `OpenStreetMap` · `GHSL` · `WIOD` · `UNCTAD` · `PMTiles` · `MapLibre`

---

## 🎬 Video demo

<!--
GitHub native video player:
1. Open this README in the GitHub web editor.
2. Drag trailer.mp4 into the editor.
3. GitHub will upload it and generate a github.com/user-attachments/assets/... URL.
4. Replace the placeholder URL below with that generated URL.
5. Leave the URL on its own line — do not wrap it in Markdown link syntax.
-->

https://github.com/user-attachments/assets/288b2763-5534-4263-8c75-e7d6c8b660a0

<p align="center">
  <sub>54-second demo · Saudi Arabia → Switzerland oil trade · multimodal routing · counterfactual disruption · re-routing</sub>
</p>

### What you see in the demo

**A global transport network.**  
The opening view combines the geographic layers used by the model into one network. Countries are represented through population centres, which connect economic activity to real locations on the transport graph.

**Population centres, roads and railways.**  
Population data are used to create a small set of representative centres for each country. Road and rail infrastructure is extracted from OpenStreetMap, converted into connected graphs, then simplified so that routing remains computationally manageable while preserving economically relevant connectivity.

**Air transport.**  
Freight airports act as gateway nodes between domestic land networks and international air transport. This allows air transport to compete with other modes when it is economically plausible for the selected product and trade flow.

**Ports, chokepoints and the maritime network.**  
Ports connect inland infrastructure to the maritime layer, while strategic passages such as the Strait of Hormuz are represented explicitly. This makes it possible to apply shocks to meaningful pieces of infrastructure rather than to abstract country-level trade costs.

**Simulating Saudi Arabia → Switzerland oil trade.**  
The model takes an exporter, importer and product, distributes the national flow across population-centre pairs, predicts modal shares, and constructs a portfolio of feasible multimodal routes. It then reports the realized modal mix, route portfolio and average generalized transport cost.

**Counterfactual disruption.**  
Selected network links are assigned a large additional generalized cost to represent disruption. In the demo, this is used to represent the Hormuz disruption together with routes affected by the war in Ukraine.

**Re-routing and modal substitution.**  
The model solves the transport problem again under the shocked network. Routes can change within a mode, and the relative use of road, rail, maritime and air can also change when their relative costs move.

---

## 🆕 Latest update

### MARNET upgrade — rebuilding the maritime layer
**October 2026 · Work in progress**

The latest development is a major upgrade of the maritime network. The goal is to improve global sea routing around coastlines, narrow passages and chokepoints while keeping ports fully connected to the rest of the multimodal graph.

The new pipeline uses higher-resolution maritime geometry, an adaptive water-cell representation, explicit maritime gateways and chokepoints, and regional validation around difficult passages before the new network replaces the current production maritime layer.

<p align="center">
  <img src="assets/latest/marnet-01.png" width="32%" alt="MARNET upgrade overview">
  <img src="assets/latest/marnet-02.png" width="32%" alt="MARNET maritime network detail">
  <img src="assets/latest/marnet-03.png" width="32%" alt="MARNET chokepoint detail">
</p>

<p align="center">
  <sub>Latest MARNET development snapshots</sub>
</p>

---

## ⚡ How the model works

```mermaid
flowchart LR
    A["Exporter · Importer · Product"] --> B["Population-centre pairs"]
    B --> C["Predicted modal shares"]
    C --> D["Multimodal route portfolio"]
    D --> E["Average generalized cost"]
    E --> F["Infrastructure shock"]
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

The core idea is simple: **economic trade data tell the model what needs to move; the transport graph determines how it can move.**

The routing engine does not return only one shortest path. It constructs a **portfolio of alternative routes**, compares their modal composition with the modal shares predicted from observed transport data, and allocates the flow across the portfolio.

After a disruption, the same problem is solved again using the shocked network costs. The difference between baseline and counterfactual transport costs can then be passed into the economic model.

---

## 🌍 Building the global transport graph

The transport system is represented as a directed graph with geographic nodes and physical connections.

### Population centres

Economic data are generally reported at country level, but transportation occurs between real locations. To connect those two representations, each country is mapped to several population centres rather than one arbitrary geographic centroid.

Population cells are grouped into representative population basins. Each centre receives a share of the country's population and therefore a share of the national trade flow used by the routing model.

### Road and rail

OpenStreetMap roads and railway tracks are converted into connected grid-based networks. The raw network is then compressed by retaining junctions and critical nodes, collapsing degree-2 chains, and pruning structures that do not contribute to useful connectivity.

The purpose is to preserve the structure needed for global routing without carrying the full complexity of the raw OpenStreetMap network into every simulation.

### Air

Freight airports form the gateway nodes of the air layer. They connect national land infrastructure to international air transport and allow the routing model to consider air alongside road, rail and maritime transport.

### Maritime

Ports connect inland transport to the maritime network. Chokepoints are represented explicitly so that disruptions can be applied to strategic passages such as the Strait of Hormuz.

The maritime layer is currently being upgraded through the new MARNET pipeline shown in the [latest update](#-latest-update).

---

## 💰 From transport observations to network costs

The model estimates transport costs from observed transport expenditure, shipment weight and transport work.

For a transport mode \(m\), product \(g\) and distance \(D\), the line-haul component is approximated by:

\[
\hat{M}_{mg}(D)=\hat{a}_{mg}+\tilde{b}_{mg}D
\]

This makes transport costs product-specific: moving crude oil, refrigerated food and high-value manufactured goods can imply very different transport requirements.

When detailed product estimates are based on limited data, they are shrunk toward broader product or mode-level estimates rather than treated as equally reliable.

The routing engine then adds the economic cost of travel time and delay, producing an edge-level **generalized cost**:

\[
G_{e,g}=M_{e,g}+H_{e,g}
\]

This gives the routing algorithm one consistent objective that can reflect both direct monetary cost and the economic importance of time in transit.

---

## 🚚 Modal shares and route portfolios

Different transport modes compete for the same trade flow, but their attractiveness depends on the shipment.

The model predicts modal-work shares from characteristics such as shipment quantity, value density, distance, infrastructure accessibility and cargo category.

Those predicted shares are then confronted with the physical network. For every population-centre pair, the routing engine computes a shortest path and searches for additional mode-oriented alternatives.

The resulting set of routes is:

\[
\mathcal{R}_{odg}=\{r_1,r_2,\ldots,r_R\}
\]

The model allocates trade across these routes so that the realized modal mix is as close as possible to the predicted one. Among solutions with an equally good modal fit, it chooses the lower-cost portfolio.

> **The statistical model describes what modal mix is plausible; the transport network determines what is physically achievable.**

---

## 💥 Counterfactual disruptions

A disruption is introduced as an additional generalized cost on selected transport links:

\[
G^{1}_{e,g}=G^{0}_{e,g}+\Delta G_{e,g}
\]

This can represent a closure, severe congestion, conflict-related disruption or any other event that makes a piece of infrastructure substantially more expensive to use.

The model then recomputes the route portfolio under the shocked network.

Two responses can occur at the same time: **within-mode re-routing**, where a route changes but keeps the same transport mode, and **between-mode substitution**, where the relative use of road, rail, maritime or air also changes.

The result is a new set of routes, modal shares and average generalized transport costs that can be compared directly with the baseline.

---

## 📈 From physical disruption to economic impact

The transport model ultimately produces changes in bilateral, product-specific trade costs.

These changes form the bridge between the physical network and the economic model.

The broader thesis uses an international trade framework with sectoral production linkages so that higher transport costs can propagate through import prices, sourcing decisions, intermediate inputs, production costs, expenditure and trade flows.

The objective is therefore not only to answer:

> *Where will the cargo go instead?*

but also:

> **What are the economic consequences when global trade has to go another way?**

---

## 📊 Data and scale

The project combines economic, geographic and transport datasets at very different scales.

**WIOD / WIOT** provides the international production, intermediate-input and final-demand structure used by the economic model.

**UNCTAD transport-cost data** provides transport expenditure, shipment weight, transport work and modal information used for transport-cost and modal-share inference.

**OpenStreetMap** provides the road and rail infrastructure. **GHSL** provides population geography used to construct population centres. **GEBCO** supports the current maritime-network upgrade. Additional port, airport, chokepoint and maritime-reference datasets connect the individual transport layers.

The cost-inference pipeline is designed for very large datasets. The working UNCTAD extraction contains approximately **863 million raw observations across 2016–2019**, so estimation is performed through data chunking and sufficient statistics rather than loading the full dataset into memory.

---

## 🧠 What this project demonstrates

This project combines **international trade economics**, **network science**, **geospatial data engineering**, **constrained optimization**, **large-scale data processing**, and **interactive visualization** in one end-to-end counterfactual simulation.

<details>
<summary><strong>Technical detail · population-centre construction</strong></summary>

Countries are represented by several geographic population centres rather than a single centroid.

Populated cells are assigned to population-weighted centres on the sphere, producing population basins. Each final centre receives a national population share that is later used to distribute bilateral trade across origin–destination centre pairs.

This keeps the economic model consistent at country level while introducing realistic geography into the transport problem.

</details>

<details>
<summary><strong>Technical detail · network simplification</strong></summary>

The raw road and rail networks are much denser than required for global routing.

After rasterizing the relevant infrastructure, the model keeps critical nodes and graph junctions, collapses degree-2 chains and prunes structures that do not contribute to connectivity between critical locations.

Bridge detection is used when identifying dangling network components that can be removed without destroying economically relevant connectivity.

</details>

<details>
<summary><strong>Technical detail · route portfolio optimization</strong></summary>

For every origin–destination–product pair, the model predicts modal-work shares, computes the generalized-cost shortest path, searches for additional mode-oriented alternatives, calculates each route's modal composition, and allocates transport across routes to minimize modal-share mismatch.

Among allocations with the same minimum mismatch, a second optimization selects the lower-cost route portfolio.

</details>

<details>
<summary><strong>Technical detail · shocked routing</strong></summary>

Selected edges receive an exogenous generalized-cost increase.

The model rebuilds the route portfolio, measures relative modal cost changes, updates modal shares using estimated substitution parameters, and solves the route-allocation problem again.

Baseline and counterfactual outputs can then be compared directly.

</details>

---

## 🚧 Project status

This repository is a **research demo** of an active Master's thesis.

The goal of this public repository is to make the model, its intuition and its counterfactual mechanism understandable without requiring the full research environment or the underlying large-scale datasets.

**Current focus:** MARNET maritime-network upgrade and validation.

---

## 👤 Author

**Marco Meyer**  
Master's in Economics · University of Zurich  
Minor in Quantitative Finance

[LinkedIn](https://www.linkedin.com/in/meyeramarco/)

---

<div align="center">

### From physical disruption to economic impact

**Transport networks · Trade costs · Counterfactuals · Global economics**

</div>
