---
title: What is the economic cost of soil compaction? Initial estimation via geophysical mapping and continental agroecosystem modelling
description: Soil compaction is a threat with negative impacts on soil ecosystem services, such as food production, groundwater recharge and climate regulation. This study discusses an approach to estimate them by combining (1) continental-based agroecosystem simulations of compaction induced economic losses and (2) case study-based quantification of soil compaction spatial extent with geophysical methods.
---

# Abstract

Soil compaction is a threat with negative impacts on soil ecosystem services, such as food production, groundwater recharge and climate regulation. The extent and severity of the environmental and economic impacts of compaction remain challenging to quantify. This study discusses an approach to estimate them by combining (1) continental-based agroecosystem simulations of compaction induced economic losses and (2) case study-based quantification of soil compaction spatial extent with geophysical methods. To illustrate this, we used two case studies of compacted fields mapped with electromagnetic induction methods in Germany (20% of area compacted) and Belgium (25% of area compacted), together with continental simulations from a soil-compaction-agroecosystem model. The simulations predict changes in crop yield, nitrogen losses and soil carbon stocks from compacted arable land across Europe under the high emissions Shared Socioeconomic Pathway (SSP585) scenario considering winter wheat, the most cultivated crop in Europe. These changes are subsequently converted to economic losses based on literature values of their corresponding soil ecosystem services loss. The predicted costs result from an ensemble of three compaction levels (representing wheel loads of 4, 6 and 8 Mg), five coupled climate models, and a twenty year long period (Near Future, NF; from 2020-2040). Coupling simulation results to the case-study maps lead to estimations of economic losses of 28.2 ±10.6 €ha-1yr-1 for the field in Germany and 21.6 ±13 €ha-1yr-1 for the field in Belgium. Assuming the percentages of compacted fields hold across these countries, this leads to an estimated annual cost of soil compaction of 454 and 38 M€ for Germany and Belgium, respectively. This framework offers an initial assessment of region/farm specific soil compaction induced economic losses in cropland that further incentivize soil health and ecosystem service protection.
--- 
title: Interactive Soil Compaction Impact Analysis
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
kernelspec:
  display_name: Python 3
  name: python3
---
# Interactive Soil Compaction Impact in Europe

This visualization shows economic and environmental losses due to soil compaction across Europe.
Interact with the sliders to explore data at different locations and compaction scenarios.

## Interactive Plot

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt
from ipywidgets import interact, FloatSlider, FloatText, widgets
import pandas as pd
import scipy.io

# --- Load Data (Replace with your actual paths) ---
# Load the mat file with coordinates
mat = scipy.io.loadmat('dataAbstract/mapEUROPE.mat')  # Adjust path
MAPeu = mat['MAPeu']

# Load coordinate data
df = pd.read_csv("dataAbstract/out1costo.txt", header=None, sep=r"\s+")
x_coords = df[0].values
y_coords = df[1].values

# Load the new MATLAB file with 5 variables
data_input = scipy.io.loadmat('dataAbstract/datain100_v7.mat')
var_names = [k for k in data_input.keys() if not k.startswith('__') and k != 'MAPeu']
var_names = [var_names[i] for i in [0, 4, 1, 2, 3]]  # Select 5 variables

Z_vars = {}
for i, var_name in enumerate(var_names):
    var_data = data_input[var_name]
    if len(var_data.shape) == 1:
        var_data = var_data.reshape(-1, 1)
    Z_vars[f'Z{i+1}'] = var_data

z_key2 = [
    r'Economic losses (€ $\mathrm{ha}^{-1}\,\mathrm{yr}^{-1})$',
    r'Yield losses (Mg $\mathrm{ha}^{-1}\,\mathrm{yr}^{-1})$',
    r'C losses (Mg $\mathrm{ha}^{-1}\,\mathrm{yr}^{-1})$',
    r'$\mathrm{N}_{2}O$ losses (kg $\mathrm{ha}^{-1}\,\mathrm{yr}^{-1})$',
    r'$\mathrm{NO}_{3}$ losses (kg $\mathrm{ha}^{-1}\,\mathrm{yr}^{-1})$'
]

def plot_colormap_with_histograms(alpha, vmin, vmax, center_x, center_y):
    # (Your existing plotting function here)
    # [Paste the full `plot_colormap_with_histograms` function from your code]

# --- Interactive Widget ---
alpha = FloatText(value=0.20, description='%Compacted', disabled=False)
vmin = FloatSlider(min=-25, max=300, step=0.1, value=0, description='min €')
vmax = FloatSlider(min=-25, max=300, step=0.1, value=50, description='max €')
center_x = FloatSlider(min=-12, max=40, step=0.5, value=6.49, description='Longitude')
center_y = FloatSlider(min=33, max=73, step=0.5, value=51.069861, description='Latitude')

ui = widgets.VBox([
    widgets.HBox([alpha, vmin, vmax]),
    widgets.HBox([center_x, center_y])
])

out = widgets.interactive_output(
    plot_colormap_with_histograms,
    {'alpha': alpha, 'vmin': vmin, 'vmax': vmax, 'center_x': center_x, 'center_y': center_y}
)

display(ui, out)

