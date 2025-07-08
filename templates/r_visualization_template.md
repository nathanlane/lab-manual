# R Visualization Template

Produce publication-quality figures with a standardized look and reproducible pipeline.

---

## Setup

```r
# 0_setup_plots.R
library(tidyverse)
library(ggthemes)

# Global theme
theme_set(theme_bw(base_size = 12))

default_colors <- c("#1b9e77", "#d95f02", "#7570b3")
```

---

## Data Prep Pipeline

```r
# 1_prep_data.R
source("0_setup_plots.R")

df <- read_csv("../data/input/trade.csv") %>%
  mutate(real_exports = exports / cpi)

write_csv(df, "../data/intermediate_datasets/trade_clean.csv")
```

---

## Multi-Panel Figure

```r
# 2_make_figure.R
source("0_setup_plots.R")

df <- read_csv("../data/intermediate_datasets/trade_clean.csv")

p1 <- df %>%
  ggplot(aes(year, real_exports, color = country)) +
  geom_line(size = 1) +
  scale_color_manual(values = default_colors) +
  labs(x = NULL, y = "Real Exports", title = "Exports over Time")

p2 <- df %>%
  ggplot(aes(real_exports, gdp_growth, color = country)) +
  geom_point() +
  scale_color_manual(values = default_colors) +
  labs(x = "Real Exports", y = "GDP Growth")

library(patchwork)
figure <- p1 / p2 + plot_layout(guides = 'collect')

# Export
fig_path <- "../output/figures/exports_figure.pdf"
ggsave(fig_path, figure, width = 6, height = 8)
```

---

## Metadata

Alongside every exported figure, create a `.yml` file with metadata:

```yaml
# exports_figure.yml
figure: exports_figure.pdf
description: "Relationship between real exports and GDP growth"
author: Jane Doe
date: 2024-05-15
version: v01
input_data: trade_clean.csv
```

---

☑️ Track figures and metadata in `output/figures/` and rerun scripts via `source("2_make_figure.R")`. Ensure your environment is clean (no leftover objects) before generating figures.