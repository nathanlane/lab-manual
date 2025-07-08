# Stata Guide

A concise yet comprehensive reference for writing clean, reproducible Stata code in empirical economics projects.

---

## 📂 Project Organization

```text
project/
└── code/
    ├── 0_setup.do        <- Install packages, set globals/paths
    ├── 1_clean_data.do   <- Transform raw data
    ├── 2_analysis.do     <- Run estimations & save results
    ├── 3_output.do       <- Export tables & figures
    └── master.do         <- Calls the scripts above in order
```

`master.do` should be the *only* script that a new RA needs to run to reproduce results.

---

## ✍️ Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Variables | lowercase with underscores | `l_grossoutput` |
| Programs  | descriptive verbs | `prep_data`, `export_reg_results` |
| Tempfiles | use `tempfile` & meaningful names | `tempfile merged` |
| Scripts   | numeric prefixes for execution order | `02_merge_trade.do` |

---

## 📝 Documentation Style

```stata
*------------------------------------------------------------------------------*
* What this does:
*   [Clear description of purpose]
*
* Dependencies:
*   - [List required packages]
*
* Inputs:
*   - [List input files with paths]
*
* Outputs:
*   - [List output files with descriptions]
*------------------------------------------------------------------------------*
```

Place this block **at the top** of every `.do` file.

---

## 🔑 Best Practices

1. **Clean Slate** – Start scripts with:
   ```stata
   capture log close _all
   clear all
   set more off
   ````
2. **Drop Before Define** – Always `capture program drop myprog` before creating a program.
3. **Centralize Paths** – Define globals for data and output paths *once* in `0_setup.do`.
4. **Use Locals** – Store constants (e.g., lists of variables) in local macros.
5. **Validate Data** – After merges, run `assert _merge == 3` and summarize key variables.
6. **Log Everything** – Wrap scripts in `log using "../output/logs/1_clean_data.log", replace`.

---

## ⚙️ Example `master.do`

```stata
/*******************************************************************
 Master script to reproduce the paper "Trade Shocks & Wages".
 Place this file at project root and execute: do master.do
*******************************************************************/

// 1. Setup -------------------------------------------------------------------
do "code/0_setup.do"

// 2. Data Preparation ---------------------------------------------------------
do "code/1_clean_data.do"

// 3. Analysis -----------------------------------------------------------------
do "code/2_analysis.do"

// 4. Output -------------------------------------------------------------------
do "code/3_output.do"

// End -------------------------------------------------------------------------
```

---

## 📚 Additional Resources

- [Stata Style Guide: World Bank](https://github.com/worldbank/Stata-Style-Guide)
- [Ado-lint](https://github.com/wbuchanan/ado-lint) – Linter for Stata code.