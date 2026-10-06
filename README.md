<div align="center">

# Modeling the Economic Impact of Trade Disruptions

### From physical transport shocks to changes in prices, demand, and production

**Master's Thesis · Economics · University of Zurich**

<br>

[![Status](https://img.shields.io/badge/STATUS-ACTIVE%20DEVELOPMENT-2da44e?style=for-the-badge)](#latest-update--marnet-upgrade)
[![Demo](https://img.shields.io/badge/DEMO-54%20SECONDS-1f6feb?style=for-the-badge)](#video-demo)
[![Focus](https://img.shields.io/badge/FOCUS-TRADE%20DISRUPTIONS-30363d?style=for-the-badge)](#research-objective)

<br>

**[Objective](#research-objective) · [Demo](#video-demo) · [Latest update](#latest-update--marnet-upgrade) · [Architecture](#model-architecture) · [Transport model](#transport-model) · [Economic model](#economic-impact) · [Data](#data-and-scale)**

</div>

<br>

---

## Research objective

### Quantifying how transport disruptions propagate through the economy

A disruption to a port, railway, border crossing, maritime chokepoint, or other piece of transport infrastructure changes **how goods move**, **what transport costs**, and **where transport revenue is generated**.

The objective of this thesis is to translate those physical disruptions into measurable economic effects on:
- prices
- demand 
- production
- trade flows
- transport-sector activity

The project combines two connected models:

**Geographic routing model**  
A global multimodal transport graph representing road, rail, air, and maritime infrastructure. It converts a physical disruption into changes in bilateral, product-specific transport costs and country/mode transport-sector revenues.

**Computable General Equilibrium (CGE) model**  
An international trade model with sectoral production linkages. It takes the transport shock and propagates it through import prices, sourcing decisions, intermediate inputs, production costs, expenditure, demand, and output.

> [!NOTE]
> **Model flow:** Physical disruption → re-routing and modal substitution → trade-cost & transport-revenue changes → economic impact

<br>

### Core stack

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat-square&logo=numpy&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-8CAAE6?style=flat-square&logo=scipy&logoColor=white)
![graph-tool](https://img.shields.io/badge/graph--tool-Graph%20Algorithms-8250DF?style=flat-square)
![Kuzu](https://img.shields.io/badge/Kuzu-Graph%20Database-6E40C9?style=flat-square)
![GeoPandas](https://img.shields.io/badge/GeoPandas-Geospatial-139C5A?style=flat-square)
![Rasterio](https://img.shields.io/badge/Rasterio-Raster%20Processing-0A7E8C?style=flat-square)
![MapLibre](https://img.shields.io/badge/MapLibre-Visualization-396CB2?style=flat-square)

**Data & infrastructure:** OpenStreetMap · GHSL · WIOD/WIOT · UNCTAD · GEBCO · AIS / maritime traffic data · PMTiles

<br>

---

## Video demo

### A 54-second walkthrough of the transport model

https://github.com/user-attachments/assets/c909a5d6-b1ef-4e0c-9c3e-79ca22095fcd
<p align="center">
  <sub>Saudi Arabia → Switzerland oil trade · multimodal routing · infrastructure disruption · re-routing and modal substitution</sub>
</p>

<br>

### 01 · Population centers
**From 1 km² population grids to geographic trade origins and destinations**

Economic data are available at the **country level**, while transportation takes place between real locations. Using a **1 km² population grid**, the model identifies several representative population centers for each country and assigns each one a share of national population.

These centers anchor trade geographically, avoiding the unrealistic assumption that an entire country exports and imports through a single centroid.

<br>

### 02 · Road and rail network
**Compressing OpenStreetMap infrastructure into a routable global graph**

Road and railway infrastructure is extracted from **OpenStreetMap** and converted into connected grid-based graphs. The raw networks are then simplified: important junctions are retained, long degree-2 chains are collapsed, and structures that do not contribute to economically relevant connectivity are removed.

The result preserves the network topology required for routing while making repeated global simulations computationally feasible.

<br>

### 03 · Airports and air network
**Connecting domestic infrastructure to international air transport**

Freight airports act as gateways between local road/rail infrastructure and the international air layer. Air transport can therefore compete with other modes when the shipment characteristics make it economically plausible.

<br>

### 04 · Ports, chokepoints and maritime network
**Connecting inland trade to global shipping corridors**

Ports connect domestic infrastructure to the maritime graph. Strategic passages are represented explicitly so routes can be identified through economically critical locations such as the **Strait of Hormuz**.

This also allows a disruption to be applied to the infrastructure itself rather than imposing an abstract country-level trade-cost shock.

<br>

### 05 · Simulate trade
**Saudi Arabia → Switzerland · Oil**

The national trade flow is divided across origin–destination population-center pairs. For each pair, the model predicts the expected transport-mode composition and searches the physical network for feasible routes.

An abstract bilateral trade flow is therefore translated into geographically explicit transport movements.

<br>

### 06 · Multimodal trade routes
**From one shortest path to a portfolio of feasible alternatives**

The model first predicts **modal-work shares** across road, rail, maritime, and air transport. It then computes the generalized-cost shortest path together with additional mode-oriented alternatives.

Trade is allocated across this route portfolio so that the realized modal composition remains as close as possible to the predicted shares. Among equally good modal matches, the model selects the lower-cost allocation.

**Output:** modal shares · route weights · distance · time · generalized costs · average trade cost

<br>

### 07 · Counterfactual disruption
**Applying shocks directly to selected infrastructure**

The demo increases the generalized cost of selected network links to represent disruption around the **Strait of Hormuz** and transport corridors affected by the **war in Ukraine**.

The shock is geographic: specific infrastructure becomes more expensive to use, and the transport system must adapt.

<br>

### 08 · Re-routing and modal substitution
**Recomputing the transport equilibrium after the shock**

The routing problem is solved again under the disrupted network.

**Within-mode re-routing** changes the physical route while keeping the transport mode.

**Between-mode substitution** changes the relative use of road, rail, maritime, and air when their relative costs change.

The resulting changes in generalized transport costs and transport-sector revenues are then passed to the CGE model.

<br>

---

## Latest update — MARNET upgrade

### A more realistic maritime routing layer

[![Latest](https://img.shields.io/badge/LATEST-OCTOBER%202026-1f6feb?style=flat-square)](#latest-update--marnet-upgrade)
[![Stage](https://img.shields.io/badge/STAGE-VALIDATION-d29922?style=flat-square)](#latest-update--marnet-upgrade)

The current development focus is a major redesign of the maritime network. The objective is to move beyond generic sea connectivity and build routes that better reflect **where vessels can physically navigate and where commercial traffic actually travels**.

> [!NOTE]
> The upgraded pipeline combines **bathymetry, navigation restrictions, observed traffic lanes, ports, gateways, and explicit chokepoints** before the network is integrated into the full transport model.

**Bathymetry**  
GEBCO data is used to represent the physical maritime domain and reduce routing through implausible shallow or land-constrained areas.

**Navigation restrictions**  
Coastlines, narrow channels, barriers, and other navigational constraints restrict which maritime connections are physically admissible.

**Traffic lanes**  
AIS / ship-density information identifies heavily used commercial corridors. These observations help the network reproduce realistic shipping patterns instead of relying only on geometric shortest paths.

**Ports and gateways**  
Ports are explicitly attached to the maritime graph. Narrow passages use gateway structures so that connectivity is preserved through realistic entrances and exits.

**Chokepoints**  
Strategic passages such as Suez, Panama, Gibraltar, Singapore, the Gulf of Aden, and the Turkish Straits are represented and tested explicitly because they are central to counterfactual disruption analysis.

The upgraded network is validated region by region before replacing the current production maritime layer.

<br>

<p align="center">
  <img src="assets/latest/marnet-01.png" width="31%" alt="MARNET upgrade overview">
  &nbsp;
  <img src="assets/latest/marnet-02.png" width="31%" alt="MARNET maritime routing detail">
  &nbsp;
  <img src="assets/latest/marnet-03.png" width="31%" alt="MARNET chokepoint validation">
</p>

<p align="center">
  <sub>Latest development snapshots · maritime network construction and validation</sub>
</p>

<br>

---

## Model architecture

### The transport model generates the shock; the CGE model computes the economic response

The two models solve different parts of the same problem.

**01 · Geographic routing model**

For each exporter–importer–product flow, the transport model computes a baseline and a disrupted transport solution. It provides the CGE model with:

- **bilateral, product-specific trade-cost changes**; and
- **transport-sector revenue attribution by country and mode**.

**02 · CGE model**

The CGE model takes those changes as inputs. Higher or lower delivered trade costs alter sourcing and expenditure decisions, while changes in transport activity affect the corresponding transport sectors.

Because industries use intermediate inputs from other sectors and countries, the initial transport shock propagates through the production network.

> **Transport graph:** *How does the disruption change the cost and geography of trade?*  
> **CGE model:** *How do those changes affect prices, demand, production, and trade?*

<br>

---

# Transport model

## 1 · Global transport graph

### Representing the physical system

The global transport system is represented as a directed graph:

```math
G=(V,E)
```

where $`V`$ is the set of geographic nodes and $`E`$ is the set of physical transport connections.

**Critical nodes** represent economically important locations such as population centers, ports, and airports.

**Junction nodes** preserve the topology of the infrastructure, such as road or rail intersections.

Each transport mode is first constructed separately and then connected through access and intermodal links.

<br>

## 2 · Population-center construction

### Translating country-level economic data into geography

Let $`c`$ denote a country and $`i \in \mathcal{I}_c`$ a populated 1 km² cell with population $`p_i`$ and geographic location $`x_i`$.

The represented population of the country is:

```math
P_c=\sum_{i\in\mathcal{I}_c}p_i
```

Rather than representing a country by one centroid, populated cells are assigned to several population-weighted centers. Each final population basin receives a national share:

```math
s_{ck}=\frac{P_{ck}}{P_c}
```

These shares are later used to divide national bilateral trade across geographic origin–destination pairs.

<br>

## 3 · Road and rail construction

### Preserving connectivity while reducing network complexity

OpenStreetMap represents roads and railway tracks as sequences of geographic coordinates. The selected infrastructure is rasterized into occupied grid cells, adjacent cells are connected, and geographic distance is stored on each edge.

The graph is then compressed. Nodes with degree different from two, together with critical nodes, are retained as junctions:

```math
J^m=\{v\in V^m:\deg(v)\neq2\}\cup H^m
```

Chains between junctions are collapsed while preserving total distance. Dead ends and dangling structures that do not connect relevant critical infrastructure are removed.

<br>

## 4 · Transport-cost inference

### Learning monetary transport costs from observed flows

Observed transport data provides transport expenditure $`E_n`$, transported quantity $`Q_n`$, and transport work $`W_n`$.

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

Product-specific estimates capture differences in transport requirements across goods. When detailed estimates are based on sparse or noisy data, they are reliability-adjusted toward broader product or mode-level estimates.

The resulting monetary transport-cost function is:

```math
\hat{M}_{mgt}(D)=\hat{a}_{mgt}+\tilde{b}_{mgt}D
```

<br>

## 5 · Generalized transport cost

### Combining monetary cost with the economic value of time

Routing depends on more than direct transport expenditure.

For edge $`e`$ and product $`g`$:

```math
G_{e,g}=M_{e,g}+H_{e,g}
```

where $`M_{e,g}`$ is monetary transport cost and $`H_{e,g}`$ values travel time and additional delay.

A geographically short route is therefore not necessarily the economically cheapest route.

<br>

## 6 · Modal shares

### Predicting how transport work is divided across modes

The model predicts the relative attractiveness of each mode from shipment characteristics such as quantity, value density, distance, accessibility, cargo category, and interaction effects.

For shipment $`n`$ and mode $`m`$:

```math
U_{nm}=\alpha_m+\beta_m^\top X_n
```

The attractiveness scores are converted into predicted modal-work shares:

```math
\hat{s}_{nm}
=
\frac{\exp(U_{nm})}
{\sum_k\exp(U_{nk})}
```

The statistical layer therefore provides a plausible target modal composition for the shipment.

<br>

## 7 · Route portfolio

### Reconciling predicted modal behavior with physical network constraints

For each origin–destination–product pair, the model first computes the generalized-cost shortest path:

```math
r^*
=
\arg\min_r
\sum_{e\in r}G_{e,g}
```

It then searches for additional mode-oriented alternatives:

```math
\mathcal{R}_{odg}=\{r_1,r_2,\ldots,r_R\}
```

Trade is allocated across the resulting portfolio so that the realized modal composition is as close as possible to the predicted modal shares. Among allocations with the same minimum modal mismatch, the lower-cost portfolio is selected.

> [!TIP]
> **Statistical model:** what modal mix is plausible?  
> **Physical graph:** what modal mix is actually achievable?

<br>

## 8 · Shocked routing

### Re-solving routes and modal shares after disruption

A disruption changes the generalized cost of selected edges:

```math
G_{e,g}^{1}
=
G_{e,g}^{0}
+
\Delta G_{e,g}
```

The route portfolio is then recomputed on the shocked network:

```math
r^1
=
\arg\min_r
\sum_{e\in r}G_{e,g}^{1}
```

Changes in mode-specific transport costs alter the relative attractiveness of the modes. Updated modal shares are therefore computed before the route-allocation problem is solved again.

This captures both **physical re-routing** and **modal substitution**.

<br>

---

# Economic impact

## From trade-cost changes to a new economic equilibrium

The transport model is not the final outcome of the thesis. Its purpose is to generate geographically grounded changes in trade costs and transport-sector activity.

The CGE model propagates those changes through international sourcing and input-output linkages:

> **Import prices → intermediate-input costs → production costs → demand → production → trade flows**

The final objective is to compare the baseline equilibrium with the disrupted equilibrium and quantify how a transport shock changes **prices, demand, production, trade, and transport-sector activity** across countries and sectors.

<br>

---

## Data and scale

### Combining economic, transport, and geographic data

**WIOD / WIOT**  
International production, intermediate-input, and final-demand relationships.

**UNCTAD transport-cost data**  
Transport expenditure, shipment weight, transport work, and modal information used for cost and modal-share inference.

**OpenStreetMap**  
Road, rail, coastlines, and supporting transport geography.

**GHSL**  
1 km² population grid used to construct population centers.

**GEBCO**  
Bathymetric information used in the upgraded maritime-network construction.

**AIS / maritime traffic data**  
Observed shipping-density information used to identify and validate commercial maritime corridors.

Additional port, airport, chokepoint, and navigation datasets connect and constrain the individual transport layers.

> [!NOTE]
> The working UNCTAD extraction contains approximately **863 million raw observations across 2016–2019**. Cost estimation therefore uses chunking and sufficient statistics rather than loading the full dataset into memory.

<br>

---

## What this project brings together

### Economics
International trade · CGE modeling · input-output linkages · trade costs · counterfactual analysis

### Quantitative methods
Constrained optimization · weighted regression · hierarchical shrinkage · modal-share prediction · numerical simulation

### Engineering
Graph construction · geospatial processing · large-scale data pipelines · multimodal routing · interactive visualization

<br>

---

## Project status

**Active Master's thesis research.**

This repository is the public-facing demonstration layer of the project. It is designed to explain the model, show the routing system in action, and communicate the counterfactual mechanism without requiring the full research environment or the underlying large-scale datasets.

**Current focus:** validation and integration of the upgraded MARNET maritime network.

<br>

---

<div align="center">

## Marco Meyer

**Master's in Economics · University of Zurich**  
Minor in Quantitative Finance

[LinkedIn](https://www.linkedin.com/in/meyeramarco/)

<br>

### From physical disruption to economic impact

*Transport networks · Trade costs · Counterfactuals · Global economics*

</div>
