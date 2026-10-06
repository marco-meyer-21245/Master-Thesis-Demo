<div align="center">

# Modeling the Economic Impact of Trade Disruptions

### From physical transport disruptions to changes in prices, demand, and production

**Master's Thesis · Economics · University of Zurich**

[![Status](https://img.shields.io/badge/status-active%20development-1f8a70?style=flat-square)](#latest-update)
[![Demo](https://img.shields.io/badge/demo-54%20sec-2563eb?style=flat-square)](#video-demo)

</div>

---

## Goal

The objective of this thesis is to **quantify the economic impact of trade disruptions**.

A disruption to a port, railway, border crossing, maritime chokepoint, or other piece of transport infrastructure changes how goods can move through the world. The model first measures how that disruption changes **routes, transport modes, trade costs, and transport-sector revenues**. It then feeds those changes into an economic model to estimate the resulting effects on **prices, demand, production, and trade**.

The project therefore combines two connected models:

**1. A geographic routing model**  
A global multimodal graph representing road, rail, air, and maritime transport. It converts a physical disruption into changes in bilateral, product-specific transport costs and country/mode transport-sector revenue.

**2. A Computable General Equilibrium (CGE) model**  
An international trade model with sectoral production linkages. It takes the transport shock and propagates it through import prices, sourcing decisions, intermediate inputs, production costs, expenditure, demand, and output.

In short:

> **Physical disruption → re-routing and modal substitution → trade-cost shock → economic impact**

### Core stack

`Python` · `NumPy` · `SciPy` · `graph-tool` · `Kuzu` · `GeoPandas` · `Rasterio` · `OpenStreetMap` · `GHSL` · `WIOD` · `UNCTAD` · `GEBCO` · `PMTiles` · `MapLibre`

---

## Video demo

https://github.com/user-attachments/assets/288b2763-5534-4263-8c75-e7d6c8b660a0

<p align="center">
  <sub>54-second trailer · Saudi Arabia → Switzerland oil trade · multimodal routing · counterfactual disruption · re-routing</sub>
</p>

The demo follows the transport model from the construction of the physical network to a counterfactual trade disruption.

### Population centers

Economic data are available at the **country level**, but transport takes place between real geographic locations.

Using a **1 km² population grid**, the model identifies several representative population centers for each country. Population cells are grouped into geographic basins and each center receives a share of the country's population.

These centers become the geographic origins and destinations of trade. This avoids representing an entire country by a single arbitrary centroid and allows trade to enter and leave through geographically plausible locations.

### Road and rail network

Road and railway infrastructure is extracted from **OpenStreetMap**.

The original networks are far too detailed for repeated global routing, so they are converted into connected grid-based graphs and then compressed. Junctions and economically important nodes are preserved, while long degree-2 chains are collapsed and disconnected or irrelevant structures are removed.

The result retains the connectivity required for routing while substantially reducing graph size.

### Airports and air network

Freight airports connect the land network to international air transport.

Each airport acts as a gateway between local road/rail infrastructure and the air layer. The model can therefore consider air transport alongside other modes when the characteristics of the shipment make it competitive.

### Ports, chokepoints and maritime network

Ports connect inland infrastructure to the maritime graph.

Strategic maritime passages are represented explicitly, allowing the model to identify routes through locations such as the **Strait of Hormuz** and to apply disruptions directly to the affected infrastructure.

The maritime layer is currently being upgraded through the new MARNET pipeline described in the [latest update](#latest-update).

### Simulate trade

The demo selects:

**Saudi Arabia → Switzerland · Oil**

The national trade flow is divided across origin–destination population-center pairs. For each pair, the model predicts the expected transport-mode composition and searches the physical network for feasible routes.

This turns an abstract bilateral trade flow into a set of geographically explicit transport movements.

### Multimodal trade routes

The model does not assume that all trade follows one shortest path.

It first predicts **modal-work shares** across road, rail, maritime, and air transport. It then computes a portfolio containing the generalized-cost shortest path and additional mode-oriented alternatives.

Trade is allocated across this route portfolio so that the realized modal composition is as close as possible to the predicted modal shares. Among equally good modal matches, the model selects the lower-cost allocation.

The output is a portfolio of routes with:

**modal shares · route weights · distance · time · generalized costs · average trade cost**

### Counterfactual analysis

A disruption is introduced by increasing the generalized cost of selected network links.

In the demo, selected links represent disruption around the **Strait of Hormuz** and transport corridors affected by the **war in Ukraine**.

The intervention is geographic: the shock is applied to specific infrastructure rather than directly assuming a country-level increase in trade costs.

### Re-routing and modal substitution

The routing problem is solved again under the shocked network.

The model can respond in two ways:

**Within-mode re-routing** — trade remains on the same mode but follows a different physical route.

**Between-mode substitution** — the relative attractiveness of road, rail, maritime, and air changes, causing the modal shares themselves to adjust.

The resulting changes in average generalized transport costs and transport-sector revenues are passed to the CGE model.

---

## Latest update

### MARNET upgrade

**October 2026 · Active development**

The latest development is a major redesign of the maritime routing layer.

The goal is to move from a generic maritime connectivity graph toward a network that better reflects **where vessels can physically navigate and where they actually tend to travel**.

The upgraded maritime pipeline combines several types of information:

**Bathymetry.**  
GEBCO bathymetric data helps distinguish navigable water from shallow or physically implausible passages and supports more realistic routing around coastlines and narrow channels.

**Navigation restrictions.**  
Maritime routing is constrained by geographic and navigational restrictions so that routes do not simply follow mathematically short but operationally invalid paths.

**Traffic lanes.**  
Observed maritime traffic information is used to recover major shipping corridors and encourage routes to follow realistic vessel traffic patterns rather than arbitrary open-ocean shortcuts.

**Ports and gateways.**  
Ports are explicitly connected to the maritime graph and narrow passages are represented through gateway structures that preserve connectivity while forcing routes through realistic entrances and exits.

**Chokepoints.**  
Strategic passages such as Suez, Panama, Gibraltar, Singapore, the Gulf of Aden, and the Turkish Straits are explicitly represented and validated because they are central to counterfactual disruption analysis.

The upgrade is currently being validated region by region before replacing the existing production maritime layer.

<p align="center">
  <img src="assets/latest/marnet-01.png" width="32%" alt="MARNET upgrade overview">
  <img src="assets/latest/marnet-02.png" width="32%" alt="MARNET maritime routing detail">
  <img src="assets/latest/marnet-03.png" width="32%" alt="MARNET chokepoint validation">
</p>

<p align="center">
  <sub>Latest MARNET development snapshots</sub>
</p>

---

## How the two models connect

The geographic model and the CGE model solve different parts of the problem.

### Geographic routing model

For each exporter–importer–product flow, the routing model computes a baseline transport solution and a shocked transport solution.

It provides the economic model with two main outputs:

**Trade-cost changes**  
How much the generalized cost of moving a particular product between two countries changes after the disruption.

**Transport-sector revenue attribution**  
Where transport expenditure is generated across countries and transport modes along the selected routes.

### CGE model

The CGE model takes these transport changes as inputs.

Higher or lower trade costs alter the delivered price of imported goods. Firms and consumers adjust their sourcing and expenditure decisions. Because industries use intermediate inputs from other sectors and countries, the effect propagates through the production network.

The CGE model then solves for the new equilibrium in:

**prices · demand · production · trade flows · transport-sector activity**

This is the central connection of the thesis:

> The transport graph determines **how the physical disruption changes trade costs**.  
> The CGE model determines **what those trade-cost changes do to the economy**.

---

## Transport graph

The global transport system is represented as a directed graph:

```math
G=(V,E)
```
where $`V`$ is the set of geographic nodes and $`E`$ is the set of physical transport connections.

The graph contains two broad types of nodes:

**Critical nodes** represent economically important locations such as population centers, ports, and airports.

**Junction nodes** preserve the topology of transport infrastructure, such as road or rail intersections.

Each transport mode is initially constructed separately and is later connected through access and intermodal links.

---

## Population-center construction

Let $`c`$ denote a country and $`i \in \mathcal{I}_c`$ a populated 1 km² cell with population $`p_i`$ and geographic location $`x_i`$.

The represented population of the country is:

```math
P_c=\sum_{i\in\mathcal{I}_c} p_i
```
A country is represented by several population centers rather than a single point. Populated cells are assigned to their nearest center and population-weighted centroids are iteratively updated until the centers converge.

Each final population basin receives a share:

```math
s_{ck}=\frac{P_{ck}}{P_c}
```
These shares are later used to divide national bilateral trade across geographic origin–destination pairs.

---

## Road and rail construction

OpenStreetMap represents roads and railway tracks as sequences of geographic coordinates.

The infrastructure is rasterized into occupied grid cells. Adjacent occupied cells are connected into a graph and geographic distances are stored on the resulting edges.

The graph is then compressed. Nodes with degree different from two, together with critical nodes, are retained as junctions:

```math
J^m=\{v\in V^m:\deg(v)\neq 2\}\cup H^m
```
Chains between junctions are collapsed while preserving their total distance.

Dead ends and dangling structures that do not connect critical infrastructure are removed, reducing the graph substantially while retaining the topology relevant for trade routing.

---

## Transport-cost inference

Observed transport data provides:

- transport expenditure $`E_n`$,
- transported quantity $`Q_n`$,
- transport work $`W_n`$.

The implied expenditure per tonne is:

```math
M_n=\frac{E_n}{Q_n}
```
and the implied transport distance is:

```math
D_n=\frac{W_n}{Q_n}
```
For transport mode $`m`$ and product group $`g`$, the model estimates:

```math
M_n=a_{mgt}+b_{mgt}D_n+u_n
```
Product-specific estimates are useful because different goods have different transport requirements. When detailed estimates are based on limited data, they are reliability-adjusted toward broader mode or product estimates.

The estimated monetary transport cost is then:

```math
\hat{M}_{mgt}(D)=\hat{a}_{mgt}+\tilde{b}_{mgt}D
```
---

## Generalized transport cost

Routing depends not only on monetary expenditure but also on transport time.

For an edge $`e`$ and product $`g`$, generalized cost is:

```math
G_{e,g}=M_{e,g}+H_{e,g}
```
where $`M_{e,g}`$ is the monetary transport cost and $`H_{e,g}`$ values travel time and delay.

This means that a physically short route is not necessarily economically optimal if it is slow, delayed, or relies on expensive transport infrastructure.

---

## Modal shares

The model predicts the relative attractiveness of each transport mode using shipment characteristics such as quantity, value density, distance, accessibility, and cargo category.

For shipment $`n`$ and mode $`m`$:

```math
U_{nm}=\alpha_m+\beta_m^\top X_n
```
The score is converted into predicted modal-work shares using:

```math
\hat{s}_{nm}
=
\frac{\exp(U_{nm})}
{\sum_k \exp(U_{nk})}
```
The statistical model therefore provides a plausible target modal composition for each shipment.

---

## Route portfolio

For each origin–destination–product pair, the model first computes the generalized-cost shortest path:

```math
r^*
=
\arg\min_r
\sum_{e\in r}G_{e,g}
```
It then searches for additional mode-oriented alternatives, producing a route portfolio:

```math
\mathcal{R}_{odg}=\{r_1,r_2,\ldots,r_R\}
```
The model allocates the flow across these routes so that the realized modal composition is as close as possible to the predicted modal shares.

Among route allocations with the same minimum modal mismatch, it chooses the lower-cost portfolio.

> **The statistical model says what modal mix is plausible; the transport graph determines what is physically achievable.**

---

## Shocked routing

A disruption changes the generalized cost of selected edges:

```math
G_{e,g}^{1}
=
G_{e,g}^{0}
+
\Delta G_{e,g}
```
The route portfolio is then recomputed using the shocked network:

```math
r^1
=
\arg\min_r
\sum_{e\in r}G_{e,g}^{1}
```
Changes in mode-specific transport costs alter the relative attractiveness of transport modes. Updated modal shares are therefore computed before the portfolio-allocation problem is solved again.

This allows the transport system to adapt through both physical re-routing and modal substitution.

---

## Economic propagation

The output of the transport model is not the final result of the thesis.

Its role is to produce a geographically grounded trade-cost shock and transport-sector revenue allocation for the economic model.

The CGE model then propagates these changes through international sourcing and input-output linkages.

A disruption can therefore affect:

**import prices**

→ **intermediate-input costs**

→ **production costs**

→ **consumer and firm demand**

→ **production**

→ **trade flows**

The final objective is to compare the baseline equilibrium with the disrupted equilibrium and quantify the economic consequences of the transport shock.

---

## Data and scale

The project combines several large economic and geographic datasets.

**WIOD / WIOT** provides international production, intermediate-input, and final-demand relationships.

**UNCTAD transport-cost data** provides transport expenditure, shipment weight, transport work, and modal information for cost and modal-share inference.

**OpenStreetMap** provides road and rail infrastructure.

**GHSL** provides the 1 km² population grid used to construct population centers.

**GEBCO** provides bathymetric information for the maritime-network upgrade.

Additional airport, port, chokepoint, maritime-traffic, and navigation datasets connect and constrain the transport layers.

The transport-cost inference pipeline is designed for very large datasets. The working UNCTAD extraction contains approximately **863 million raw observations across 2016–2019**, so estimation is performed through chunking and sufficient statistics instead of loading the entire dataset into memory.

---

## What this project brings together

**Economics**  
International trade · CGE modeling · input-output linkages · trade costs · counterfactual analysis

**Quantitative methods**  
Constrained optimization · weighted regression · hierarchical shrinkage · modal-share prediction · numerical simulation

**Engineering**  
Graph construction · geospatial processing · large-scale data pipelines · multimodal routing · interactive visualization

---

## Project status

This repository is a public-facing research demo of an active Master's thesis.

It is designed to explain the model and demonstrate its counterfactual mechanism without requiring access to the full research environment or the underlying large-scale datasets.

**Current focus:** validation and integration of the upgraded MARNET maritime network.

---

## Author

**Marco Meyer**  
Master's in Economics · University of Zurich  
Minor in Quantitative Finance

[LinkedIn](https://www.linkedin.com/in/meyeramarco/)

---

<div align="center">

### From physical disruption to economic impact

**Transport networks · Trade costs · Counterfactuals · Global economics**

</div>
