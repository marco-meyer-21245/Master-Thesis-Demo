<div align="center">

# Modeling the Economic Impact of Trade Disruptions

### From physical transport shocks to changes in prices, demand, and production

**Master's Thesis · Economics · University of Zurich**

<br>

[![Status](https://img.shields.io/badge/STATUS-ACTIVE%20DEVELOPMENT-2da44e?style=for-the-badge)](#latest-updates)
[![Demo](https://img.shields.io/badge/DEMO-54%20SECONDS-1f6feb?style=for-the-badge)](#video-demo)
[![Focus](https://img.shields.io/badge/FOCUS-TRADE%20DISRUPTIONS-30363d?style=for-the-badge)](#research-objective)

<br>

**[Objective](#research-objective) · [Demo](#video-demo) · [Latest updates](#latest-updates) · [Architecture](#model-architecture) · [Transport model](#transport-model) · [Economic model](#economic-impact) · [Data](#data-and-scale) · [References](#selected-references)**

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

The economic framework draws on **origin-differentiated demand** ([Armington, 1969](https://doi.org/10.2307/3866403)), the importance of **sectoral heterogeneity in trade** ([Ossa, 2015](https://doi.org/10.1016/j.jinteco.2015.07.002)), and **input-output linkages in quantitative trade models** ([Caliendo & Parro, 2015](https://doi.org/10.1093/restud/rdu035)).

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

## Latest updates

Two improvements to the transport model: a more geographically faithful maritime network and a recalibrated, production-integrated routing and cost system.

### October 2026 · Maritime network (MARNET)

#### Navigable shipping corridors with a smaller, more accurate graph

The previous maritime representation was too coarse around canals, coastlines and narrow passages. The upgraded **MARNET** construction combines **GEBCO bathymetry**, **OpenStreetMap waterways and navigation restrictions**, and **AIS vessel-density data** (global, commercial, and oil & gas traffic).

Instead of using the same resolution across the entire ocean, the pipeline uses **finer cells around constrained passages and ports** and **larger cells in open water**. It then simplifies the graph while protecting important maritime connections and preserving routed distances.

This design is related to work on **bathymetry-based adaptive maritime grids** ([Choi, Lee & Kim, 2023](https://doi.org/10.1093/jcde/qwad055)) and **AIS-derived shipping networks** ([Liu et al., 2023](https://doi.org/10.1016/j.oceaneng.2022.113494)). These are methodological precedents, not validation of this specific network.

**What changed**

- **Bathymetry and navigability:** account for shallow water, coastlines, rivers, canals, and restricted connections.
- **Observed traffic corridors:** use vessel-density data to inform commercially relevant sea routes, without treating density as a calibrated probability of route choice.
- **Ports and chokepoints:** attach ports and explicitly preserve narrow passages, including Hormuz, Suez, Gibraltar, Singapore, and Panama.
- **Adaptive simplification:** remove unnecessary offshore detail without replacing realistic maritime paths with straight-line shortcuts.

**Measured results — experimental global adaptive graph**

| Metric | Before | After |
|:--|--:|--:|
| Vertices | 4,715,627 | **2,114,825** (−55.2%) |
| Edges | 12,126,688 | **6,004,848** (−50.5%) |

The reported global simplification and validation run took **about 267 seconds** with **13.3 GB peak memory**. The candidate retained **1,983 attached ports** and **27 of 28 chokepoints**; the Bering Strait attachment remains unresolved.

Shortest-path checks against the detailed graph found approximately **+0.1%** distance change at Hormuz, **+0.5%** at Suez, and **+3.4%** at Panama — showing both the accuracy of most tested passages and the remaining challenge around constrained canals.

<p align="center">
  <a href="https://github.com/user-attachments/assets/2d659501-6a95-4ab5-96a2-a32257ea222c"><img width="48%" alt="Maritime network update — image 1" src="https://github.com/user-attachments/assets/2d659501-6a95-4ab5-96a2-a32257ea222c"></a>
  <a href="https://github.com/user-attachments/assets/d622862b-bebb-4a2b-8d2a-8a88b1bdc9dd"><img width="48%" alt="Maritime network update — image 2" src="https://github.com/user-attachments/assets/d622862b-bebb-4a2b-8d2a-8a88b1bdc9dd"></a>
</p>
<p align="center">
  <a href="https://github.com/user-attachments/assets/65aafc14-0985-4d09-a0ca-21793d9325f6"><img width="48%" alt="Maritime network update — image 3" src="https://github.com/user-attachments/assets/65aafc14-0985-4d09-a0ca-21793d9325f6"></a>
  <a href="https://github.com/user-attachments/assets/3a7a77f6-6ddd-4d6b-b9ff-793f21e5f194"><img width="48%" alt="Maritime network update — image 4" src="https://github.com/user-attachments/assets/3a7a77f6-6ddd-4d6b-b9ff-793f21e5f194"></a>
</p>
<p align="center"><sub>MARNET development images · click an image for the full-resolution view</sub></p>

> [!NOTE]
> **Implementation status:** The 2.11-million-vertex adaptive graph is a separately staged and validated **experimental candidate**, not the same artifact as the maritime layer in the deployed V5 multimodal graph. Integration and further navigational validation remain ongoing.

<br>

### October 2026 · Transport-cost inference and pathing (V5)

#### From independently observed freight costs to validated multimodal routing

The second update connects empirical freight-market evidence with a corrected physical routing system. The previous model relied too heavily on indirect transport-cost estimates, and some road, rail and customs costs produced implausible route or modal choices.

**Cost inference.** The new V5 model combines independent freight evidence — **CNR and Eurostat for road, US STB waybills for rail, BTS Form 41 for air, and UNCTAD maritime freight reports** — with UNCTAD **CIF–FOB** observations. Bayesian regularization helps retain economically plausible coefficients where the data cannot separately identify fixed, distance-related, and value-related costs.

The approach builds on the distinction between directly observed and inferred **trade costs** ([Anderson & van Wincoop, 2004](https://doi.org/10.1257/0022051042177649)), the **economic value of transport time** ([Hummels & Schaur, 2013](https://doi.org/10.1257/aer.103.7.2935)), and general principles of **prior-based regularization** ([Gelman et al., 2008](https://doi.org/10.1214/08-AOAS191)). The specific V5 likelihood and priors are implementation choices, not specifications taken directly from those papers.

**Pathing and accounting.** The routing system now retains feasible alternatives for each customs-entry mode instead of only the few globally cheapest paths. The update also corrects **border-cost accounting**, **customs-entry mode attribution**, **historical transit restrictions**, and several geographic connectivity issues. These changes help distinguish real modal substitution from artifacts of an incomplete transport graph.

**Measured calibration and validation**

| Evaluation | Result |
|:--|--:|
| Dominant-mode accuracy, main 103-cell A+B evaluation sample | **86.4%** |
| Dominant-mode accuracy, held-out origin–destination pairs | **81.5%** |
| Dominant-mode accuracy, 2019 temporal holdout | **86.4%** |
| 2019 air CIF–FOB log error, previous → posterior V5 | **1.025 → 0.962** |

On the main evaluation sample, the predicted **road share was 8.6%** against **8.4% observed**, and the predicted **sea share was 79.8%** against **78.5% observed**. Correcting country-code mappings also increased 2016 processed trade observations from **8.08 million to 10.00 million**.

The posterior cost estimates improve the economic grounding of the model. They do **not** improve every predictive metric: sea CIF–FOB error on the origin–destination holdout changed from **0.537 to 0.548** (worse), while the 2019 sea error changed from **0.525 to 0.522** (slightly better).

**Deployment — 8 October 2026.** The validated V5 pipeline was promoted to the canonical transport graph and visualization workflow:

**495,818 routing vertices · 4,320,068 routing edges · 408 population centers · 225 tests passed, 1 skipped**

This deployment updates the routing graph, origin–destination context, cost models and eight map-tile layers. It establishes a more reproducible link between observed freight costs, physically feasible route choices, and the trade-cost shocks required by the economic model.

> [!NOTE]
> Out-of-sample tests cover held-out country pairs and the year 2019. The strictest observed-mode sample remains small, and some UNCTAD mode labels are inferred or model-filled; these results should not be interpreted as validation on a large independently measured shipment dataset.

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

Here `V` represents geographic nodes and `E` represents the physical connections between them.

**Critical nodes** represent economically important locations such as population centers, ports, and airports.

**Junction nodes** preserve the topology of the infrastructure, such as road or rail intersections.

Each transport mode is first constructed separately and then connected through access and intermodal links.

<br>

## 2 · Population-center construction

### Translating country-level economic data into geography

Let `c` denote a country and `i` a populated 1 km² cell within that country. Each cell has a population (`p_i`) and a geographic location (`x_i`).

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

### Combining freight-market evidence with UNCTAD observations

The current **V5 monetary cost model** is calibrated with both independent freight-market evidence and UNCTAD CIF–FOB observations. Road, rail, air and maritime data provide empirical priors; a Bayesian posterior updates them while regularizing parameters that are weakly identified.

The simplified per-tonne monetary specification is:

```math
M_{n}=\alpha_{mg}+b_{mg}D_n+\rho_{mg}v_n+u_n
```

Here `D_n` is the network-based transport distance and `v_n` is goods value per tonne. The coefficients represent fixed cost (α), the distance-related rate (b), and the value-related component (ρ). These parameters vary by mode and cargo class, with uncertainty taken into account.

The underlying raw data also records shipment weight (`Q_n`), transport work (`W_n`), and expenditure (`E_n`). For observations providing these quantities, implied distance and expenditure per tonne can be expressed as:

```math
D_n=\frac{W_n}{Q_n}, \qquad M_n=\frac{E_n}{Q_n}
```

Observed expenditure and generalized route cost are not interchangeable: the routing objective additionally incorporates time, border, access and transfer costs, with accounting controls intended to avoid double counting.

<br>

## 5 · Generalized transport cost

### Combining monetary cost with the economic value of time

Routing depends on more than direct transport expenditure.

For a transport-network edge `e` and product `g`:

```math
G_{e,g}=M_{e,g}+H_{e,g}
```

The first term is monetary transport cost; the second assigns an economic value to travel time and additional delay.

A geographically short route is therefore not necessarily the economically cheapest route.

<br>

## 6 · Modal shares

### Predicting how transport work is divided across modes

The model predicts the relative attractiveness of each mode from shipment characteristics such as quantity, value density, distance, accessibility, cargo category, and interaction effects.

For an observation `n` and transport mode `m`:

```math
U_{nm}=\alpha_m+\beta_m^\top X_n
```

The attractiveness scores are converted into predicted modal-work shares:

```math
\hat{s}_{nm} = \frac{\exp(U_{nm})} {\sum_k\exp(U_{nk})}
```

The statistical layer therefore provides a plausible target modal composition for the shipment.

<br>

## 7 · Route portfolio

### Reconciling predicted modal behavior with physical network constraints

For each origin–destination–product pair, the model first computes the generalized-cost shortest path:

```math
r^* = \arg\min_r \sum_{e\in r}G_{e,g}
```

It then searches for additional mode-oriented alternatives:

```math
\mathcal{R}_{odg}=\{r_1,r_2,\ldots,r_R\}
```

Trade is allocated across the resulting portfolio so that the realized modal composition is as close as possible to the predicted modal shares. Among allocations with the same minimum modal mismatch, the lower-cost portfolio is selected.

For an earlier example of combining a **geographic freight network, generalized costs, and modal allocation**, see [Beuthe et al. (2001)](https://doi.org/10.1016/S1366-5545(00)00022-3).

> [!TIP]
> **Statistical model:** what modal mix is plausible?  
> **Physical graph:** what modal mix is actually achievable?

<br>

## 8 · Shocked routing

### Re-solving routes and modal shares after disruption

A disruption changes the generalized cost of selected edges:

```math
G_{e,g}^{1} = G_{e,g}^{0} + \Delta G_{e,g}
```

The route portfolio is then recomputed on the shocked network:

```math
r^1 = \arg\min_r \sum_{e\in r}G_{e,g}^{1}
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

## Selected references

The papers below provide the **economic foundations and methodological precedents** most directly related to the model. They do not imply that the thesis reproduces each paper's implementation or that its empirical validation is inherited from those studies.

### Economic model

- **Armington, P. S. (1969).** [A Theory of Demand for Products Distinguished by Place of Production](https://doi.org/10.2307/3866403). *IMF Staff Papers*, 16(1), 159–178. — Origin-differentiated goods and substitution in international trade.
- **Ossa, R. (2015).** [Why Trade Matters After All](https://doi.org/10.1016/j.jinteco.2015.07.002). *Journal of International Economics*, 97(2), 266–277. — Sectoral heterogeneity and the economic importance of trade in critical industries.
- **Caliendo, L., & Parro, F. (2015).** [Estimates of the Trade and Welfare Effects of NAFTA](https://doi.org/10.1093/restud/rdu035). *Review of Economic Studies*, 82(1), 1–44. — Quantitative trade counterfactuals with intermediate inputs and sectoral production linkages.

### Maritime network and multimodal routing

- **Choi, G.-H., Lee, W., & Kim, T.-W. (2023).** [Voyage Optimization Using Dynamic Programming with Initial Quadtree Based Route](https://doi.org/10.1093/jcde/qwad055). *Journal of Computational Design and Engineering*, 10(3), 1185–1203. — Adaptive bathymetric maritime-network construction.
- **Liu, L., et al. (2023).** [Data-Driven Framework for Extracting Global Maritime Shipping Networks by Machine Learning](https://doi.org/10.1016/j.oceaneng.2022.113494). *Ocean Engineering*, 269, 113494. — Maritime network extraction from AIS observations.
- **Beuthe, M., et al. (2001).** [Freight Transportation Demand Elasticities: A Geographic Multimodal Transportation Network Analysis](https://doi.org/10.1016/S1366-5545(00)00022-3). *Transportation Research Part E*, 37(4), 253–266. — Generalized-cost multimodal routing and freight mode allocation.

### Trade costs and empirical calibration

- **Anderson, J. E., & van Wincoop, E. (2004).** [Trade Costs](https://doi.org/10.1257/0022051042177649). *Journal of Economic Literature*, 42(3), 691–751. — Measurement and interpretation of trade costs.
- **Hummels, D. L., & Schaur, G. (2013).** [Time as a Trade Barrier](https://doi.org/10.1257/aer.103.7.2935). *American Economic Review*, 103(7), 2935–2959. — The economic value of delivery time in freight transport.
- **Gelman, A., Jakulin, A., Pittau, M. G., & Su, Y.-S. (2008).** [A Weakly Informative Default Prior Distribution for Logistic and Other Regression Models](https://doi.org/10.1214/08-AOAS191). *Annals of Applied Statistics*, 2(4), 1360–1383. — General principles of weakly informative priors and regularization; not the specific V5 prior specification.

<br>

---

## Project status

**Active Master's thesis research.**

This repository is the public-facing demonstration layer of the project. It is designed to explain the model, show the routing system in action, and communicate the counterfactual mechanism without requiring the full research environment or the underlying large-scale datasets.

**Latest progress:** V5 transport-cost inference and multimodal routing promoted on 8 October 2026; experimental adaptive MARNET construction and simplification validated separately, with further integration work ongoing.

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
