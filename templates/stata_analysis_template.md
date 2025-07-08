# Stata Analysis Template

Use this modular framework to organize Stata code for a typical empirical paper. Each program performs a focused task and writes outputs for post-processing.

---

## Project Skeleton

```text
code/
├── 0_setup.do
├── 1_clean_data.do
├── 2_generate_vars.do
├── 3_regressions.do
├── 4_export_output.do
└── master.do
```

---

## 0_setup.do

```stata
*------------------------------------------------------------------------------*
* 0_setup.do - Install packages, set globals, and initialize log              *
*------------------------------------------------------------------------------*
clear all
set more off

* Paths
if ("`c(os)'" == "Windows") global projdir "C:/Projects/myproject" // adjust
else                        global projdir "~/myproject"

global codedir  "$projdir/code"
set linesize 80

* Install required packages
ssc install estout, replace
ssc install reghdfe, replace
```

---

## 1_clean_data.do

```stata
* Clean raw data and save to intermediate_datasets
use "$projdir/data/input/raw_survey.dta", clear

* Variable renaming
rename (income wage) (household_income hourly_wage)

* Keep complete cases
keep if !missing(household_income, hourly_wage)

save "$projdir/data/intermediate_datasets/survey_clean.dta", replace
```

---

## 3_regressions.do (Excerpt)

```stata
capture program drop run_regs
program define run_regs
    syntax , OUTFILE(string)

    reghdfe hourly_wage household_income age i.year, absorb(industry) vce(cluster id)
    esttab using "`outfile'", replace se star(* 0.10 ** 0.05 *** 0.01)
end

run_regs , outfile("$projdir/output/tables/wage_regs.tex")
```

---

☑️ Combine everything in `master.do` as shown in the [Stata Guide](../languages/stata.md#⚙️-example-masterdo).