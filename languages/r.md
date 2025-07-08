# R Guide

This guide applies [Google's R Style Guide](https://google.github.io/styleguide/Rguide.html) to empirical economics projects.

---

## 📂 Recommended Project Layout (RStudio)

```text
project/
├── project.Rproj        <- RStudio project file
├── renv.lock            <- Dependency lockfile (optional)
└── code/
    ├── 0_setup.R        <- Load libraries, define paths
    ├── 1_clean_data.R   <- Data wrangling with tidyverse
    ├── 2_analysis.R     <- Estimation & models
    ├── 3_figures.R      <- Visualization via ggplot2
    └── 4_tables.R       <- Export regression tables
```

---

## ✍️ Naming Conventions

| Item | Convention | Example |
|------|------------|---------|
| Variables | snake_case | `tax_long`, `gg_kdb` |
| Functions | verb_noun | `save_plot()`, `clean_directory()` |
| Scripts | numeric prefixes | `02_merge_trade.R` |
| Packages | explicit at top | `library(tidyverse)` |

---

## 📝 Script Header Template

```r
## =============================================================================
# PURPOSE:   [Clear description]
# INPUTS:    [List inputs]
# OUTPUTS:   [List outputs]
# AUTHOR:    Your Name <you@uni.edu>
# CREATED:   2024-05-15
## =============================================================================
```

---

## 🔑 Best Practices

1. **Tidyverse First** – Use `dplyr`, `tidyr`, `purrr` for data manipulation.
2. **Explicit Libraries** – Load packages at the top of every script.
3. **Functional Programming** – Wrap repeated tasks in functions and, where possible, map over lists.
4. **Use `here()`** – Build file paths relative to project root for portability.
5. **Consistent Plot Themes** – Set a global theme with `theme_set()`.
6. **Unit Tests** – Place tests in `tests/` and use `testthat::test_that()`.

---

## ⚙️ Example: Cleaning Data with Tidyverse

```r
library(tidyverse)

clean_employment <- function(raw_path, out_path) {
  read_csv(raw_path) %>%
    janitor::clean_names() %>%
    filter(year >= 2000) %>%
    mutate(real_wage = wage / cpi) %>%
    write_csv(out_path)
}

clean_employment("../data/input/employment.csv",
                "../data/output/employment_clean.csv")
```

---

## 🔍 Testing Example

```r
# tests/test-clean.R
library(testthat)
source("../code/1_clean_data.R")

test_that("real wage is positive", {
  df <- read_csv("../data/output/employment_clean.csv")
  expect_true(all(df$real_wage > 0))
})
```

Run all tests via:
```r
testthat::test_dir("tests")
```

---

## 📚 Further Reading

- [R for Data Science](https://r4ds.hadley.nz/)
- [The tidyverse style guide](https://style.tidyverse.org/)