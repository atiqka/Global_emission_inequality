# Global Carbon Inequality: Lorenz Curve Analysis

Author: Atiq Kainan Ahmed. Email: atiqka@yahoo.com

This repository contains a Python-based analytical pipeline to measure and visualize global $\text{CO}_2$ emissions inequality across countries. By combining macroeconomic population data with the Our World in Data (OWID) $\text{CO}_2$ dataset, this project moves beyond traditional income-group comparisons to highlight structural differences between higher-emitting and lower-emitting nations.

---

## Methodology

### 1. Data Acquisition & Harmonization
* **Emissions Data:** The pipeline dynamically fetches the latest release of the [Our World in Data (OWID) CO₂ Dataset](https://github.com/owid/co2-data). It filters the dataset to the most recent available year ($t_{\max}$) and drops records missing `co2_per_capita`.
* **Macroeconomic & Population Data:** A local macroeconomic dataset (`macro6_data_17Jan2025.csv`) provides country-level population (`pop`) and World Bank income group classifications (`inc_grp`).
* **Data Cleaning & Merging:** Country names in both datasets are standardized by stripping leading/trailing whitespace. An inner join is performed on `countryname` to ensure that only matched, complete cases with valid population and per-capita emission figures are retained for analysis.

### 2. Mathematical Formulation
To account for vast differences in country sizes, all inequality metrics are **population-weighted**.

For a set of $N$ countries sorted in ascending order of per-capita $\text{CO}_2$ emissions ($e_1 \le e_2 \le \dots \le e_N$), where country $i$ has population $p_i$ and total emissions $E_i = e_i \cdot p_i$:

* **Cumulative Population Share ($X_k$):**
  $$X_k = \frac{\sum_{i=1}^{k} p_i}{\sum_{i=1}^{N} p_i}$$

* **Cumulative Emissions Share ($Y_k$):**
  $$Y_k = \frac{\sum_{i=1}^{k} (e_i \cdot p_i)}{\sum_{i=1}^{N} (e_i \cdot p_i)}$$

* **Lorenz Curve & Gini Coefficient:** Plotting $(X_k, Y_k)$ from $(0,0)$ to $(1,1)$ yields the Lorenz curve. The **Gini coefficient ($G$)** quantifies the area between the $45^\circ$ Line of Equality ($Y = X$) and the Lorenz curve:
  $$G = 1 - \sum_{k=1}^{N} (X_k - X_{k-1})(Y_k + Y_{k-1})$$
  where $G \in [0, 1]$, with $0$ representing perfect equality (every person emits the exact same amount) and $1$ representing maximum inequality.

### 3. Analytical Framing Design
Traditional analyses often facet countries by World Bank income groups, which measures *internal* inequality within income brackets rather than global carbon concentration. This pipeline implements four distinct framings:

1. **Global Carbon Lorenz Curve:** Sorts all matched countries globally by `co2_per_capita` to capture the overarching global distribution of emissions across the entire world population.
2. **Emission-Tier Faceting:** Replaces income brackets with explicit physical emission thresholds—Low ($<3\text{t}$), Moderate ($3\text{--}8\text{t}$), High ($8\text{--}15\text{t}$), and Very High ($>15\text{t}$)—computing the internal population-weighted Gini coefficient within each tier via `ineqpy`.
3. **Comparative Bloc Overlay:** Splits the global dataset at the median country-level per-capita emission value ($e_{\text{median}}$) into a *Higher-Emitting Bloc* ($e_i \ge e_{\text{median}}$) and a *Lower-Emitting Bloc* ($e_i < e_{\text{median}}$), overlaying their respective internal Lorenz curves on a single $[0, 1]$ axis.
4. **Combined Synthesis:** Maps the emission-tier segments directly onto the global Lorenz trajectory using color-coded segments, while superimposing the bloc-level curves as dashed reference lines.

---

## Visual Outputs

* `lorenz_framing1_global.png` — Global Carbon Lorenz Curve.
* `lorenz_framing2_emission_tiers.png` — $2 \times 2$ Facet Grid by Per-Capita Emission Tiers.
* `lorenz_framing3_comparative_overlay.png` — High vs. Low Emitter Bloc Overlay.
* `lorenz_framing4_combined.png` — Combined All-in-One Synthesis Plot.

---

## 📈 Narrative of Results

* **Global Concentration (Framing 1):** The global carbon Lorenz curve bows sharply away from the $45^\circ$ line of equality. A disproportionately large share of the global population resides in the flatter, lower-left portion of the curve, contributing a relatively small fraction of total global $\text{CO}_2$ emissions. Conversely, the curve steepens dramatically in the upper deciles, where high-emitting nations drive the bulk of cumulative global emissions.
* **Intra-Tier Dynamics (Framing 2):** When countries are grouped by per-capita emission thresholds rather than income, internal inequality (measured by the Gini coefficient) is notably lower within the *Moderate* and *High* emitter tiers compared to the *Low* emitter tier. The *Low Emitters ($<3\text{t}$)* facet captures a wide spectrum of developing nations ranging from near-zero per-capita emissions to industrializing economies approaching $3\text{t}$.
* **Bloc Comparison (Framing 3):** Overlaying the Higher-Emitting Bloc ($\ge \text{median}$) and Lower-Emitting Bloc ($< \text{median}$) reveals that internal inequality is often more pronounced within the Lower-Emitting Bloc than within the Higher-Emitting Bloc, as the latter clusters around high-consumption industrial baselines.
* **Synthesized Trajectory (Framing 4):** The combined plot pinpoints the exact cumulative population thresholds where each emission tier takes over. The transition from green ($<3\text{t}$) to orange ($3\text{--}8\text{t}$) and red/indigo ($>8\text{t}$) visually demonstrates how rapidly cumulative emissions accelerate once the curve enters the upper-emitting tiers.

---

## Interpretation of Results

1. **Why Income Groups Can Be Misleading:**
   Traditional World Bank income classifications (based on GNI per capita) frequently mask carbon inequality because high-income service economies with clean energy grids (e.g., nuclear or hydro) can have lower per-capita emissions than upper-middle-income fossil-fuel exporters. Faceting by **emission tiers** isolates carbon intensity directly.
2. **The "Tail" Drives the Total:**
   The steepness of the upper-right segment in Framing 1 and Framing 4 underscores that global decarbonization efforts cannot rely solely on broad, uniform targets. Because the top emission tiers account for a massive share of cumulative $\text{CO}_2$, policy interventions targeting the $>8\text{t}$ and $>15\text{t}$ per-capita tiers yield the highest marginal reductions in global emissions.
3. **Development vs. Decarbonization:**
   The wider internal spread within the *Low Emitters* tier reflects countries at very different stages of energy access and industrialization. For nations in this tier, climate policy must balance poverty alleviation and energy expansion with leapfrogging toward low-carbon infrastructure.

---

## Data Sources & Requirements

* **$\text{CO}_2$ Data:** [Our World in Data (OWID) $\text{CO}_2$ Dataset](https://github.com/owid/co2-data) (automatically fetched via raw GitHub URL for the latest available year).
* **Macro & Population Data:** Local CSV (`macro6_data_17Jan2025.csv`) containing `countryname`, `inc_grp`, and `pop`.

### Dependencies
```bash
pip install pandas numpy matplotlib ineqpy
```

### Usage
Update `MACRO_FILE` and `OUTPUT_DIR` in the script to match your local environment, then run:
```bash
python lorenz_analysis.py
```

