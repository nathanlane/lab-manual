# **Stata Programming Best Practices: Starter Guide**

A complete guide to writing clean, reproducible Stata code for researchers.

---

## **Table of Contents**

1. [Introduction & Quick Start](#1-introduction--quick-start)
2. [Project Organization and File Structure](#2-project-organization-and-file-structure)
3. [Data Management Best Practices](#3-data-management-best-practices)
4. [Code Style and Documentation](#4-code-style-and-documentation)
5. [Reproducibility Techniques](#5-reproducibility-techniques)
6. [Workflow Automation](#6-workflow-automation)
7. [Quality Assurance](#7-quality-assurance)
8. [Collaboration and Sharing](#8-collaboration-and-sharing)
9. [Appendices](#9-appendices)

---

## 1. **Introduction & Quick Start**

### 1.1 Purpose and Scope

This manual provides comprehensive guidelines for writing clean, efficient, and reproducible Stata code. It covers everything from project setup to collaboration, with practical examples throughout.

### 1.2 How to Use This Manual

- **New users**: Start with sections 1-3 for fundamentals
- **Intermediate users**: Focus on sections 4-6 for advanced techniques
- **Team leads**: Sections 7-8 cover collaboration and quality control

### 1.3 Quick Setup Checklist

```stata
* Essential first lines for any .do file
clear all
set more off
version 17.0
```

---

## 2. **Project Organization and File Structure**

### 🔑 **Key Principles:**

- Clearly separate raw data, processed data, scripts, and outputs
- Use consistent file naming conventions
- Favor relative paths over absolute paths
- Centralize and define paths once
- Employ standardized project templates

### 📁 **Standard Directory Structure:**

```text
project/
├── data/
│   ├── raw/
│   └── processed/
├── code/
│   ├── 0_setup.do
│   ├── 1_clean_data.do
│   ├── 2_analysis.do
│   ├── 3_output.do
│   └── master.do
└── output/
    ├── tables/
    ├── figures/
    └── logs/
```

### **Setup Script Template (`0_setup.do`)**

```stata
/******************************************************************
Purpose: Project setup and path definitions
Author: Your Name
Date: YYYY-MM-DD
*******************************************************************/

* Set version
version 17.0

* Define project root
global project_root "."

* Define paths
global raw_data "$project_root/data/raw"
global clean_data "$project_root/data/processed"
global code "$project_root/code"
global output "$project_root/output"
global tables "$output/tables"
global figures "$output/figures"
global logs "$output/logs"

* Create directories if they don't exist
foreach dir in raw_data clean_data code output tables figures logs {
    capture mkdir "${`dir'}"
}

* Set other preferences
set more off
set varabbrev off
set linesize 120
set matsize 10000
```

### **Master File Template (`master.do`)**

```stata
/******************************************************************
Purpose: Master file to run entire analysis
Author: Your Name
Date: YYYY-MM-DD
*******************************************************************/

* Change to project directory and run
cd "path/to/project"

* Run all scripts in order
do "code/0_setup.do"
do "code/1_clean_data.do"
do "code/2_analysis.do"
do "code/3_output.do"
```

### 🖥️ **Quick Start:**

Always run scripts from `master.do` at the project root:

```stata
do "code/master.do"
```

### **File Naming Conventions**

- Scripts: `[number]_[verb]_[noun].do` (e.g., `1_clean_data.do`)
- Data: `YYYYMMDD_dataname_version.dta`
- Outputs: `table_[number]_[description].tex`
- Logs: `YYYYMMDD_scriptname.log`

### 🚨 **Common Pitfalls & Solutions:**

- ❌ Avoid absolute paths: `"C:/Users/John/Documents/project"`
- ✅ Use relative paths: `"../data/raw/survey.dta"`
- ❌ Don't mix raw and processed data in same folder
- ✅ Keep clear separation between data stages

---

## 3. **Data Management Best Practices**

### 🔑 **Key Principles:**

- Never alter raw datasets directly
- Create audit trails by logging transformations
- Use clear, consistent variable names
- Label variables and values explicitly
- Document data changes thoroughly

### **Variable Naming and Labeling**

```stata
* Variable labeling
label variable wage "Hourly Wage in USD"
label variable educ "Years of Education"
label variable exp "Years of Work Experience"
label variable female "Female (1=Yes, 0=No)"

* Value labels
label define gender_lbl 0 "Male" 1 "Female"
label values gender gender_lbl

label define educ_cat 1 "Less than HS" 2 "HS Diploma" 3 "Some College" 4 "Bachelor+" 
label values education_cat educ_cat

* Yes/No labels
label define yesno 0 "No" 1 "Yes"
label values employed married union_member yesno
```

### **Data Cleaning Workflow**

```stata
* Import and initial inspection
use "$raw_data/survey_data.dta", clear
describe
codebook, compact

* Create audit trail
generate date_modified = "$S_DATE"
generate time_modified = "$S_TIME"
generate user_modified = "$S_username"
notes: Data cleaned on $S_DATE by $S_username

* Basic cleaning
* Handle missing values
mvdecode _all, mv(-99 -98 -97=.)
label define missing_lbl .a "Don't know" .b "Refused" .c "Not applicable"

* Clean and validate age
replace age = . if age < 0 | age > 120
assert inrange(age, 0, 120) if !missing(age)

* Create derived variables
gen age_squared = age^2
label var age_squared "Age squared"

gen log_wage = ln(wage) if wage > 0
label var log_wage "Natural log of hourly wage"
```

### 🖥️ **Quick Start: Merge Verification**

```stata
* Always verify merges immediately
use "$clean_data/main_data.dta", clear
merge 1:1 id using "$raw_data/demographics.dta"

* Check merge results
assert _merge == 3  // Stop if not all matched
tab _merge

* Alternative: keep only matched
use "$clean_data/main_data.dta", clear
merge 1:1 id using "$raw_data/demographics.dta", keep(match) nogen
```

### **Missing Data Handling**

```stata
* Document missing patterns
misstable summarize
misstable patterns wage educ exp

* Create missing indicators
foreach var of varlist wage educ exp {
    gen miss_`var' = missing(`var')
    label var miss_`var' "Missing indicator for `var'"
}

* Multiple imputation setup (if needed)
mi set wide
mi register imputed wage educ
mi impute regress wage educ exp female, add(20) rseed(12345)
```

### **Data Validation Checklist**

- [ ] Check variable types and formats
- [ ] Verify value ranges and outliers
- [ ] Document missing data patterns
- [ ] Confirm unique identifiers
- [ ] Test merge quality
- [ ] Validate skip patterns and logic
- [ ] Check for duplicate observations

### 🚨 **Common Pitfalls:**

- Forgetting to log data transformations
- Unclear or missing variable labels
- Not checking merge quality
- Inconsistent missing value coding

---

## 4. **Code Style and Documentation**

### 🔑 **Key Principles:**

- Comment liberally but meaningfully
- Consistent indentation (2-4 spaces)
- Meaningful macro and variable names
- Divide complex tasks into simpler blocks
- Use header templates for all `.do` files

### 📋 **File Header Template:**

```stata
/******************************************************************
Purpose: Clean employment data from CPS
Inputs: 
    - raw/cps_2023.dta
    - raw/occupation_codes.dta
Outputs: 
    - processed/employment_clean.dta
    - logs/cleaning_log_YYYYMMDD.log
Dependencies: 0_setup.do must be run first
Author: Your Name
Date Created: YYYY-MM-DD
Last Modified: YYYY-MM-DD
Version History:
    - YYYY-MM-DD: Initial version
    - YYYY-MM-DD: Added occupation code merge
    - YYYY-MM-DD: Fixed missing value handling
*******************************************************************/
```

### **Comment Standards**

```stata
*==============================================================================
* 1. DATA IMPORT AND INITIAL CLEANING
*==============================================================================

*------------------------------------------------------------------------------
* 1.1 Import raw data
*------------------------------------------------------------------------------

* Load main dataset
use "$raw_data/survey_main.dta", clear

* Quick data inspection
describe, short
summarize

*------------------------------------------------------------------------------
* 1.2 Basic variable cleaning
*------------------------------------------------------------------------------

* Age cleaning with explanation
/* Age values outside 0-120 are coding errors based on data documentation.
   Set to missing rather than dropping to preserve sample size for 
   analyses that don't use age */
replace age = . if !inrange(age, 0, 120)

* Income transformation
gen log_income = ln(income) if income > 0  // Log transform for positive values only
label var log_income "Natural log of annual income"
```

### **Indentation and Spacing Standards**

```stata
* Good indentation example
if year >= 2020 {
    gen post_covid = 1
    label var post_covid "Post-COVID period"
    
    * Nested conditions
    if quarter >= 2 {
        gen full_lockdown = 1
        label var full_lockdown "Full lockdown period"
    }
}
else {
    gen post_covid = 0
}

* Loop indentation
foreach var of varlist wage salary bonus {
    * Summary statistics
    quietly summarize `var', detail
    
    * Store results
    scalar mean_`var' = r(mean)
    scalar med_`var' = r(p50)
    
    * Display formatted results
    display _newline "Statistics for `var':"
    display "  Mean: " %9.2f mean_`var'
    display "  Median: " %9.2f med_`var'
}

* Complex conditions with line breaks
generate eligible = 1 if age >= 18 & age <= 65 & ///
                        employed == 1 & ///
                        hours >= 20 & ///
                        !missing(wage)
```

### **Variable and Macro Naming Conventions**

```stata
* Clear, descriptive macro names
local demographic_vars age female race education marital_status
local labor_vars employed hours occupation industry union
local outcome_vars wage salary benefits job_satisfaction

* Meaningful temporary variables
tempvar income_decile wage_residual predicted_wage
egen `income_decile' = cut(income), group(10)
regress wage education experience
predict `wage_residual', residuals
predict `predicted_wage', xb

* Program-specific conventions
global DATE = strofreal(date("$S_DATE", "DMY"), "%tdCYND")
global PROJECT "minimum_wage_analysis"
```

### 🖥️ **Quick Start: Standard Setup**

Clear environment at script start:

```stata
clear all
set more off
capture log close
macro drop _all
```

### 🚨 **Common Pitfalls:**

- Over-commenting obvious code
- Under-commenting complex logic
- Inconsistent indentation
- Ambiguous variable names like `var1`, `temp`, `x`

---

## 5. **Reproducibility Techniques**

### 🔑 **Key Principles:**

- Always set seeds for random processes
- Manage Stata version explicitly
- Handle external dependencies clearly
- Use master do-files for workflow control
- Log all outputs systematically

### **Complete Environment Setup**

```stata
*==============================================================================
* REPRODUCIBLE ENVIRONMENT SETUP
*==============================================================================

* Version control
version 17.0

* Clear environment
clear all
macro drop _all
capture log close
set more off

* Set randomization seeds
set seed 12345      // For random numbers
set sortseed 12345  // For stable sorts

* System settings for reproducibility
set type double     // Prevent precision issues
set maxvar 32767    // If using many variables
set matsize 11000   // For large matrices

* Display system information for log
display "Analysis run on $S_DATE at $S_TIME"
display "Stata version: `c(stata_version)'"
display "OS: `c(os)' `c(machine_type)'"
display "User: `c(username)'"
```

### 📋 **Comprehensive Logging System**

```stata
* Create timestamped log
local logdate = string(date("$S_DATE", "DMY"), "%tdCY-N-D")
local project_name "wage_analysis"
log using "$logs/`project_name'_`logdate'.log", replace text

* Add session information to log
display _newline(2) "========================================"
display "PROJECT: `project_name'"
display "DATE: $S_DATE"
display "TIME: $S_TIME"
display "========================================" _newline(2)

* Your analysis code here...

* Close log with summary
display _newline(2) "========================================"
display "Analysis completed at $S_TIME"
display "========================================" _newline(2)
log close
```

### **Random Number Management**

```stata
* For sampling - always set seed first
set seed 12345
sample 10  // Takes 10% random sample

* For bootstrap
set seed 12345
bootstrap r(mean), reps(1000): summarize wage

* For simulations
set seed 12345
program define simulate_data
    clear
    set obs 1000
    gen id = _n
    gen treatment = runiform() < 0.5
    gen outcome = rnormal(100, 15) + 10*treatment + rnormal()
end

* Run simulation
simulate_data
```

### **Version Control Integration**

```stata
* Check Stata version
if `c(stata_version)' < 17 {
    display as error "This code requires Stata version 17 or higher"
    exit 198
}

* .gitignore template for Stata projects
/* Add to .gitignore:
*.log
*.dta
*.gph
*.png
*.tex
!data/raw/*.dta    // Exception: keep raw data
.DS_Store          // Mac files
~$*                // Temporary files
*/
```

### **Dependency Management**

```stata
* Check and install required packages
local required_packages estout outreg2 coefplot ivreg2 ranktest
foreach package in `required_packages' {
    capture which `package'
    if _rc {
        display "Installing `package'..."
        ssc install `package'
    }
}

* Version checking for packages
which estout
which outreg2

* Create package requirement file
file open pkg_file using "required_packages.txt", write replace
foreach package in `required_packages' {
    file write pkg_file "`package'" _n
}
file close pkg_file
```

### 🖥️ **Quick Start: Minimal Reproducible Setup**

```stata
version 17.0
set seed 12345
log using "$logs/analysis.log", replace
```

### 🚨 **Common Pitfalls:**

- Forgetting to set seed before random operations
- Not logging terminal output
- Hardcoding package locations
- Missing version specifications

---

## 6. **Workflow Automation**

### 🔑 **Key Principles:**

- Use loops to eliminate repetitive code
- Write reusable programs for common tasks
- Employ macros efficiently
- Automate table and figure exports
- Build modular, maintainable code

### ⚙️ **Efficient Loop Patterns**

```stata
*==============================================================================
* LOOP EXAMPLES FOR COMMON TASKS
*==============================================================================

* Summary statistics for multiple variables
foreach var of varlist wage hours benefits {
    quietly summarize `var', detail
    display _newline "`var' statistics:"
    display "  Mean: " %9.2f r(mean)
    display "  SD: " %9.2f r(sd)
    display "  N: " %9.0f r(N)
}

* Loop with conditions
foreach var of varlist * {
    * Process only string variables
    if substr("`: type `var''", 1, 3) == "str" {
        display "Encoding `var'..."
        encode `var', gen(`var'_num)
    }
}

* Nested loops for interactions
local demographics age female education
local outcomes wage hours satisfaction
foreach demo in `demographics' {
    foreach outcome in `outcomes' {
        quietly regress `outcome' `demo' experience
        display "Effect of `demo' on `outcome': " %9.3f _b[`demo']
    }
}

* Loop over values
levelsof occupation, local(occ_codes)
foreach occ in `occ_codes' {
    preserve
        keep if occupation == `occ'
        summarize wage, detail
        scalar wage_med_`occ' = r(p50)
    restore
}
```

### **Custom Program Library**

```stata
*==============================================================================
* REUSABLE PROGRAMS
*==============================================================================

* Program: Prepare data with options
program define prep_data
    syntax using/, [keep(varlist) rename(string) sample(real 100)]
    
    use `using', clear
    
    * Apply sampling if requested
    if `sample' < 100 {
        sample `sample'
    }
    
    * Keep specified variables
    if "`keep'" != "" {
        keep `keep'
    }
    
    * Rename variables
    if "`rename'" != "" {
        `rename'
    }
    
    * Add metadata
    notes: Data prepared on $S_DATE
    compress
end

* Program: Standardized summary table
program define sum_table
    syntax varlist [if] [in], [by(varname)] [save(string)]
    
    if "`by'" != "" {
        table `by', statistic(mean `varlist') ///
                   statistic(sd `varlist') ///
                   statistic(count `varlist')
    }
    else {
        summarize `varlist', detail
    }
    
    if "`save'" != "" {
        export excel using "`save'", replace firstrow(statistics)
    }
end

* Program: Quick regression with diagnostics
program define reg_diagnostic
    syntax varlist [if] [in], [robust] [cluster(varname)]
    
    * Run regression
    regress `varlist' `if' `in', `robust' cluster(`cluster')
    
    * Post-estimation diagnostics
    estat vif
    predict residuals, resid
    swilk residuals
    drop residuals
end
```

### 📋 **Table Automation Examples**

```stata
* Automated regression tables
eststo clear
local outcomes wage log_wage hours

foreach y in `outcomes' {
    * Basic specification
    eststo m1_`y': regress `y' education experience
    
    * With controls
    eststo m2_`y': regress `y' education experience female i.race
    
    * With interactions
    eststo m3_`y': regress `y' c.education##c.experience female i.race
}

* Export formatted table
esttab m* using "$tables/regression_results.tex", ///
    replace booktabs label compress ///
    b(3) se(3) r2(3) ///
    star(* 0.10 ** 0.05 *** 0.01) ///
    title("Table 1: Regression Results") ///
    mtitles("Wage" "Log Wage" "Hours" "Wage" "Log Wage" "Hours" ///
            "Wage" "Log Wage" "Hours") ///
    mgroups("Basic Model" "With Demographics" "With Interactions", ///
            pattern(1 0 0 1 0 0 1 0 0))

* Summary statistics table
estpost summarize wage education experience female
esttab using "$tables/summary_stats.tex", ///
    cells("mean(fmt(2)) sd(fmt(2)) min(fmt(0)) max(fmt(0))") ///
    nomtitle nonumber replace label
```

### **Figure Automation**

```stata
* Batch figure creation with consistent formatting
local graph_opts title("") xtitle("") ytitle("") ///
                 graphregion(color(white)) ///
                 legend(ring(0) position(11))

* Create multiple histograms
foreach var in wage age education {
    local varlabel : variable label `var'
    histogram `var', ///
        title("Distribution of `varlabel'") ///
        xtitle("`varlabel'") ///
        ytitle("Density") ///
        graphregion(color(white))
    graph export "$figures/hist_`var'.png", replace width(1200)
}

* Automated coefficient plots
coefplot (m1_wage, label("Basic")) ///
         (m2_wage, label("With Controls")) ///
         (m3_wage, label("With Interactions")), ///
         drop(_cons) xline(0) ///
         graphregion(color(white)) ///
         legend(ring(0) position(11))
graph export "$figures/coef_comparison.png", replace
```

### 🖥️ **Quick Start: Basic Automation**

```stata
* Simple loop for summary statistics
foreach var of varlist age income education {
    summarize `var'
}
```

### 🚨 **Common Pitfalls:**

- Over-engineering simple tasks
- Not testing programs before using in loops
- Forgetting to clear previous stored results
- Misusing global vs local macros

---

## 7. **Quality Assurance**

### 🔑 **Key Principles:**

- Use assertions to validate assumptions
- Debug incrementally with targeted tests
- Implement systematic code review
- Create basic unit tests for custom programs
- Document all validation steps

### ✅ **Comprehensive Assertion Framework**

```stata
*==============================================================================
* DATA VALIDATION ASSERTIONS
*==============================================================================

* Basic data integrity checks
assert !missing(id)
assert id == id[_n-1] + 1 if _n > 1  // Sequential IDs
isid id  // Confirms ID uniquely identifies observations

* Variable range checks
assert age >= 0 & age <= 120 if !missing(age)
assert inrange(wage, 0, 1000) if !missing(wage)
assert inlist(education, 1, 2, 3, 4) if !missing(education)

* Logical consistency checks
assert wage == 0 if employed == 0
assert hours == 0 | hours >= 1 if employed == 1
assert age >= education + 5 if !missing(age, education)  // Reasonable age-education relationship

* Panel data checks
xtset id year
assert year == year[_n-1] + 1 if id == id[_n-1] & _n > 1  // No gaps in panel

* Merge validation
merge 1:1 id using "$clean_data/demographics.dta"
assert _merge == 3  // All observations matched
drop _merge

* Missing data patterns
assert !missing(wage) if employed == 1 & hours > 0
```

### **Debug Strategies**

```stata
* Incremental debugging approach
set trace on
set tracedepth 1

* Your problematic code here
* ...

set trace off

* Targeted debugging with pause
pause on
forvalues i = 1/10 {
    display "Iteration `i'"
    * Complex calculation
    if `i' == 5 {
        pause  // Stops here for inspection
    }
}
pause off

* Debug macros
local debug 1  // Set to 0 in production
if `debug' {
    display "Demographics vars: `demographic_vars'"
    display "N obs: `=_N'"
    describe
}

* Capture errors gracefully
capture confirm variable wage
if _rc {
    display as error "Variable 'wage' not found"
    exit 111
}
```

### **Unit Testing for Programs**

```stata
*==============================================================================
* UNIT TEST FRAMEWORK
*==============================================================================

* Test program for data preparation
program define test_prep_data
    quietly {
        * Setup: Create test data
        clear
        set obs 100
        gen id = _n
        gen value1 = runiform() * 100
        gen value2 = rnormal()
        gen str10 category = "A" if _n <= 50
        replace category = "B" if missing(category)
        save "test_temp.dta", replace
        
        * Test 1: Basic functionality
        prep_data using "test_temp.dta"
        assert _N == 100
        
        * Test 2: Keep option
        prep_data using "test_temp.dta", keep(id value1)
        assert c(k) == 2
        
        * Test 3: Sample option
        prep_data using "test_temp.dta", sample(50)
        assert _N == 50
        
        * Cleanup
        erase "test_temp.dta"
    }
    display "All tests passed for prep_data"
end

* Test program for regression diagnostics
program define test_regression
    quietly {
        * Generate test data
        clear
        set obs 500
        gen x1 = rnormal()
        gen x2 = rnormal() + 0.5*x1  // Some correlation
        gen y = 1 + 2*x1 + 3*x2 + rnormal()
        
        * Test regression assumptions
        regress y x1 x2
        
        * Normality of residuals
        predict resid, residuals
        swilk resid
        assert r(p) > 0.05  // Fail to reject normality
        
        * No perfect multicollinearity
        estat vif
        assert r(vif_1) < 10 & r(vif_2) < 10
        
        * Homoskedasticity
        estat hettest
        assert r(p) > 0.05
        
        drop resid
    }
    display "Regression diagnostics test passed"
end
```

### **Code Review Checklist**

```stata
* Automated code review checks
program define code_review
    syntax [, file(string)]
    
    if "`file'" == "" local file = c(filename)
    
    * Check 1: No absolute paths
    file open codefile using "`file'", read
    local linenum = 0
    file read codefile line
    while r(eof) == 0 {
        local linenum = `linenum' + 1
        if regexm("`line'", "[A-Z]:[/\\]") {
            display "Warning: Possible absolute path at line `linenum'"
        }
        file read codefile line
    }
    file close codefile
    
    * Check 2: Verify key components exist
    local required "version clear set seed"
    foreach component in `required' {
        display "Checking for `component'..."
        * Implementation details...
    }
end
```

### 📋 **Quality Assurance Checklist**

- [ ] All assertions pass without errors
- [ ] No hardcoded paths in code
- [ ] Random seeds set for all stochastic processes
- [ ] Logs capture complete output
- [ ] Comments explain all complex logic
- [ ] Variable labels are complete and clear
- [ ] Missing values handled appropriately
- [ ] Merge results verified
- [ ] Output files reproducible

### 🖥️ **Quick Start: Basic Validation**

```stata
assert age >= 0 & age <= 120
assert !missing(id)
```

### 🚨 **Common Pitfalls:**

- Skipping data validation steps
- Not testing edge cases
- Ignoring warning messages
- Inadequate error handling

---

## 8. **Collaboration and Sharing**

### 🔑 **Key Principles:**

- Write code as if someone else will run it tomorrow
- Provide comprehensive documentation
- Follow data confidentiality protocols strictly
- Use version control effectively
- Create portable, platform-independent code

### 📋 **Complete README Template**

```markdown
# Project Title: Analysis of Minimum Wage Effects on Employment

## Description
This project analyzes the impact of minimum wage changes on employment levels 
using state-level panel data from 2010-2023. We employ difference-in-differences 
and synthetic control methods.

## Data Sources
- **Primary data**: Current Population Survey (CPS) 2010-2023
  - Access: Public use files from [IPUMS](https://cps.ipums.org/)
  - Restrictions: None for public use files
- **Supplementary data**: State minimum wage database
  - Source: Department of Labor
  - Last updated: 2023-12-31

## Requirements
- **Software**: Stata 17.0 or higher (tested on 17.0 and 18.0)
- **Required packages**: 
  ```stata
  ssc install estout
  ssc install outreg2
  ssc install coefplot
  ssc install synth
  ```
- **Memory**: Minimum 8GB RAM recommended
- **OS**: Platform independent (tested on Windows 11, macOS 14, Ubuntu 22.04)

## File Structure
```
minimum_wage_analysis/
├── README.md
├── LICENSE
├── .gitignore
├── data/
│   ├── raw/
│   │   ├── cps_2010_2023.dta
│   │   └── state_minwage.csv
│   └── processed/
│       ├── analysis_data.dta
│       └── robustness_samples.dta
├── code/
│   ├── 0_setup.do
│   ├── 1_clean_cps.do
│   ├── 2_merge_minwage.do
│   ├── 3_main_analysis.do
│   ├── 4_robustness.do
│   ├── 5_figures_tables.do
│   └── master.do
├── output/
│   ├── tables/
│   ├── figures/
│   └── logs/
└── docs/
    ├── codebook.pdf
    └── analysis_plan.pdf
```

## Instructions to Reproduce

1. **Clone repository**
   ```bash
   git clone https://github.com/username/minimum_wage_analysis.git
   cd minimum_wage_analysis
   ```

2. **Obtain data**
   - Download CPS data from IPUMS (see docs/data_download_instructions.pdf)
   - Place raw data files in `data/raw/`

3. **Install Stata packages**
   ```stata
   do "code/0_setup.do"  // This will check and install required packages
   ```

4. **Run analysis**
   ```stata
   do "code/master.do"  // Runs entire pipeline
   ```

5. **Expected runtime**: ~45 minutes on standard desktop

## Key Results
- Main results in `output/tables/table_1_main_results.tex`
- Summary figures in `output/figures/`
- Log files with full output in `output/logs/`

## Authors
- Jane Doe (jdoe@university.edu) - Corresponding author
- John Smith (jsmith@university.edu)

## Citation
If you use this code, please cite:
```
Doe, J. and Smith, J. (2024). "Minimum Wage Effects on Employment: 
New Evidence from State Borders." Working Paper.
```

## License
This project is licensed under the MIT License - see LICENSE file for details.

## Acknowledgments
We thank [names] for helpful comments and suggestions.
Funding provided by [Grant agency, number].
```

### **Collaboration Code Standards**

```stata
*==============================================================================
* USER-SPECIFIC SETTINGS (for team collaboration)
*==============================================================================

* Handle different user paths
if "`c(username)'" == "jdoe" {
    global project_root "/Users/jdoe/Projects/minwage"
}
else if "`c(username)'" == "jsmith" {
    global project_root "C:/Research/minwage"
}
else {
    display as error "Unknown user: `c(username)'"
    display as error "Please add your path to setup file"
    exit 198
}

* Platform-specific settings
if "`c(os)'" == "Windows" {
    global python_path "C:/Python39/python.exe"
}
else if "`c(os)'" == "MacOSX" {
    global python_path "/usr/local/bin/python3"
}
else {
    global python_path "python3"
}

* Create user-specific .gitignore entries
* Add to .gitignore:
* user_config.do
* *.log
* .DS_Store
* Thumbs.db
```

### **Data Security and Confidentiality**

```stata
*==============================================================================
* DATA ANONYMIZATION FOR SHARING
*==============================================================================

* Remove direct identifiers
drop name ssn address phone email

* Create anonymous IDs
egen anon_id = group(state county household_id person_id)
drop state county household_id person_id

* Top/bottom coding for indirect identifiers
replace income = 999999 if income > 999999 & !missing(income)
replace age = 90 if age > 90 & !missing(age)

* Geographic aggregation
gen region = 1 if inlist(state, "ME", "NH", "VT", "MA", "RI", "CT")
replace region = 2 if inlist(state, "NY", "NJ", "PA") & missing(region)
// ... etc
drop state county zipcode

* Create public use file
preserve
    * Keep only necessary variables
    keep anon_id age_group education employed wage_category region weight
    
    * Add noise to prevent re-identification
    gen noise = rnormal(0, 0.1)
    replace weight = weight + noise if uniform() < 0.1
    drop noise
    
    * Save public version
    label data "Public use file - anonymized"
    save "$output/public_use_file.dta", replace
restore

* Document anonymization
file open doc using "$output/anonymization_log.txt", write replace
file write doc "Anonymization performed on: $S_DATE" _n
file write doc "Original N: `c(N)'" _n
file write doc "Variables removed: name ssn address..." _n
file close doc
```

### **Version Control Best Practices**

```stata
* Before committing, clean sensitive information
* Run this before git add:

* Remove any hardcoded paths
* Check: grep -r "C:\\" *.do
* Check: grep -r "/Users/" *.do

* Clear logs of sensitive info
shell find ./output/logs -name "*.log" -exec rm {} \;

* Create release version
program define prepare_release
    version 17.0
    
    * Copy code files
    shell mkdir -p "../release/code"
    shell cp code/*.do "../release/code/"
    
    * Copy documentation
    shell cp README.md "../release/"
    shell cp LICENSE "../release/"
    
    * Create minimal test data
    use "$clean_data/analysis_data.dta", clear
    sample 1  // 1% sample
    * Anonymize
    drop if _n > 1000  // Max 1000 obs
    save "../release/data/test_data.dta", replace
    
    display "Release prepared in ../release/"
end
```

### 🖥️ **Quick Start: Sharing Code**

```stata
* Add header to shared files
* Created by: Your Name
* Contact: your.email@institution.edu
* Last updated: $S_DATE
```

### 🚨 **Common Pitfalls:**

- Forgetting to anonymize data
- Hardcoding personal file paths
- Not testing on different platforms
- Missing documentation for dependencies

---

## 9. **Appendices**

### **A. One-Page Cheat Sheet**

#### **Essential Commands**

```stata
* ✅ Project Setup
clear all                      // Clear memory
set more off                   // Disable pagination
version 17.0                   // Set version
cd "project/directory"         // Set working directory

* ✅ Path Management
global data "./data"           // Define global paths
local filepath "$data/raw"     // Use in scripts

* ✅ Data Inspection
describe                       // Variable information
summarize                      // Summary statistics
codebook, compact             // Detailed variable info
misstable summarize           // Missing data patterns

* ✅ Data Cleaning
rename oldvar newvar          // Rename variables
label variable var "Label"    // Add variable labels
encode strvar, gen(numvar)    // Convert string to numeric
replace var = . if var < 0    // Set to missing

* ✅ Analysis
regress y x1 x2              // Linear regression
logit y x1 x2                // Logistic regression
xtreg y x1 x2, fe            // Fixed effects
test x1 = x2                 // Test coefficients

* ✅ Output
eststo: regress y x          // Store estimates
esttab using "table.tex"     // Export table
graph export "fig.png"       // Export graph
log using "output.log"       // Start log file
```

#### **Best Practices Summary**

- ✅ **Paths:** Always relative, centrally defined
- ✅ **Data:** Raw data immutable; document all changes
- ✅ **Comments:** Meaningful, explain why not what
- ✅ **Reproducibility:** Version control, set seeds, log everything
- ✅ **Automation:** Use loops and programs for repetitive tasks
- ✅ **Quality:** Assert assumptions, validate merges
- ✅ **Sharing:** Clear README, portable code, respect confidentiality

### **B. Common Error Messages & Solutions**

| Error Message | Likely Cause | Solution |
|--------------|--------------|----------|
| `variable not found` | Typo or variable doesn't exist | Check spelling with `describe` |
| `type mismatch` | Mixing string and numeric | Use `destring` or `encode` |
| `no observations` | All data filtered out | Check `if` conditions |
| `invalid syntax` | Command formatting error | Check help file for syntax |
| `file not found` | Wrong path or missing file | Verify path and file exists |
| `not sorted` | Data must be sorted first | Use `sort` before command |
| `repeated time values` | Panel data has duplicates | Check with `duplicates report` |
| `matsize too small` | Matrix size limit reached | `set matsize 10000` |
| `convergence not achieved` | Model fitting issues | Simplify model or check data |

### **C. Stata Command Quick Reference**

#### **Data Management**
- `import delimited` - Import CSV files
- `merge` - Combine datasets
- `append` - Stack datasets
- `reshape` - Convert between wide/long
- `collapse` - Aggregate data
- `egen` - Extended generate

#### **Analysis**
- `ttest` - T-tests
- `tabulate` - Frequency tables
- `correlate` - Correlation matrix
- `factor` - Factor analysis
- `predict` - Generate predictions
- `margins` - Marginal effects

#### **Programming**
- `forvalues` - Loop over numbers
- `foreach` - Loop over items
- `while` - Conditional loop
- `program` - Define programs
- `macro` - Define macros
- `scalar` - Define scalars

### **D. External Resources**

#### **Official Documentation**
- [Stata Documentation](https://www.stata.com/manuals/)
- [Stata Blog](https://blog.stata.com/)
- [Stata YouTube Channel](https://www.youtube.com/user/statacorp)

#### **Style Guides and Best Practices**
- [Stata Style Guide (World Bank)](https://github.com/worldbank/Stata-Style-Guide) - Comprehensive coding standards
- [DIME Analytics Coding Guide](https://dimewiki.worldbank.org/Stata_Coding_Practices)
- [J-PAL Coding Guide](https://www.povertyactionlab.org/resource/data-and-code-availability-standards)

#### **Tools and Utilities**
- [ado-lint: Linter for Stata](https://github.com/wbuchanan/ado-lint) - Automated code checking
- [Stata Visual Library](https://worldbank.github.io/Stata-IE-Visual-Library/) - Graph examples
- [SSC Archive](https://www.stata.com/support/ssc-installation/) - User-written packages

#### **Learning Resources**
- [UCLA IDRE Stata Resources](https://stats.idre.ucla.edu/stata/) - Tutorials and examples
- [Stata Journal](https://www.stata-journal.com/) - Peer-reviewed articles
- [Statalist Forum](https://www.statalist.org/) - Community support

#### **Reproducible Research**
- [Project TIER](https://www.projecttier.org/) - Teaching Integrity in Empirical Research
- [Berkeley Initiative for Transparency in the Social Sciences](https://www.bitss.org/)
- [Center for Open Science](https://www.cos.io/)

### **E. Template Library**

#### **E.1 Master Do-File Template**

```stata
/******************************************************************
MASTER FILE - Project Name
Purpose: Run all analysis files in sequence
Author: Your Name
Date: YYYY-MM-DD
*******************************************************************/

* Set project root (update this path)
cd "/path/to/project"

* Run setup
do "code/0_setup.do"

* Data preparation
do "code/1_import_data.do"
do "code/2_clean_data.do"
do "code/3_construct_variables.do"

* Analysis
do "code/4_descriptive_stats.do"
do "code/5_main_analysis.do"
do "code/6_robustness_checks.do"

* Output
do "code/7_create_tables.do"
do "code/8_create_figures.do"

* Cleanup
do "code/9_cleanup.do"

display "Analysis complete!"
```

#### **E.2 Data Cleaning Template**

```stata
/******************************************************************
DATA CLEANING
Purpose: Clean and prepare raw data for analysis
Input: $raw_data/datafile.dta
Output: $clean_data/datafile_clean.dta
*******************************************************************/

* Setup
version 17.0
clear all
set more off

* Load data
use "$raw_data/datafile.dta", clear

* Initial inspection
describe
summarize
misstable summarize

* Cleaning steps
* 1. Rename variables
rename var1 meaningful_name

* 2. Label variables
label variable meaningful_name "Clear description"

* 3. Handle missing values
mvdecode _all, mv(-99 -98=.)

* 4. Create derived variables
gen log_income = ln(income) if income > 0

* 5. Validate
assert !missing(id)
isid id

* Save
compress
label data "Cleaned dataset - $S_DATE"
save "$clean_data/datafile_clean.dta", replace

* Documentation
notes: Cleaned by $S_username on $S_DATE
```

#### **E.3 Analysis Template**

```stata
/******************************************************************
MAIN ANALYSIS
Purpose: Primary regression analysis
Input: $clean_data/analysis_data.dta
Output: Tables and figures in respective folders
*******************************************************************/

* Setup
version 17.0
clear all
set more off
estimates clear

* Load data
use "$clean_data/analysis_data.dta", clear

* Set panel structure (if applicable)
xtset id time

* Descriptive statistics
estpost summarize $outcome_vars $control_vars
esttab using "$tables/desc_stats.tex", replace ///
    cells("mean sd min max") nomtitle nonumber

* Main regressions
* Model 1: Baseline
eststo m1: regress y x1 x2

* Model 2: With controls
eststo m2: regress y x1 x2 $controls

* Model 3: With fixed effects
eststo m3: xtreg y x1 x2 $controls, fe

* Export results
esttab m1 m2 m3 using "$tables/main_results.tex", ///
    replace b(3) se(3) ///
    star(* 0.10 ** 0.05 *** 0.01) ///
    title("Main Results") ///
    label booktabs

* Figures
coefplot m1 m2 m3, drop(_cons) xline(0)
graph export "$figures/coef_plot.png", replace
```

---

## **Sample Workflow**

```stata
* Step 1: Set up project structure
do "code/0_setup.do"

* Step 2: Clean and prepare data
do "code/1_clean_data.do"

* Step 3: Run main analysis
do "code/2_analysis.do"

* Step 4: Generate outputs
do "code/3_output.do"

* All steps automated via:
do "code/master.do"
```

---

## 📚 **Further Learning Resources**

### **Core References**
- [Stata Style Guide (World Bank)](https://github.com/worldbank/Stata-Style-Guide) - Essential coding standards
- [ado-lint: Linter for Stata](https://github.com/wbuchanan/ado-lint) - Automated code quality checking
- [UCLA IDRE Stata Resources](https://stats.idre.ucla.edu/stata/) - Comprehensive tutorials and examples

### **Additional Resources**
- [DIME Wiki](https://dimewiki.worldbank.org/Stata_Coding_Practices) - Development Impact Evaluation practices
- [Stata Visual Library](https://worldbank.github.io/Stata-IE-Visual-Library/) - Professional graph examples
- [Project TIER](https://www.projecttier.org/) - Reproducible research protocols

---

*This manual is a living document. Please contribute improvements via pull requests or issues on our GitHub repository.*
