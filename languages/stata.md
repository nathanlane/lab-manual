📝 Stata Programming Best Practices

A comprehensive guide to writing clean, reproducible Stata code tailored for junior researchers.

⸻

📂 1. Project Organization and File Structure

🔑 Key Principles:
	•	Clearly separate raw data, processed data, scripts, and outputs.
	•	Use consistent file naming conventions.
	•	Favor relative paths over absolute paths.
	•	Centralize and define paths once.
	•	Employ standardized project templates.

📁 Recommended Directory Structure:

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

🖥️ Quick Start:

Always run scripts from master.do at the project root:

do "code/master.do"

🚨 Common Pitfalls:
	•	Avoid absolute paths (e.g., "C:/Users/...").
	•	Don’t mix raw and processed data.

⸻

📊 2. Data Management Best Practices

🔑 Key Principles:
	•	Never alter raw datasets directly.
	•	Create audit trails by logging transformations.
	•	Use clear, consistent variable names.
	•	Label variables and values explicitly.
	•	Document data changes thoroughly.

📋 Examples:

label variable wage "Hourly Wage in USD"
label define gender_lbl 0 "Female" 1 "Male"
label values gender gender_lbl

🖥️ Quick Start:

Always verify merges immediately:

merge 1:1 id using "../data/raw/dataset.dta"
assert _merge == 3

🚨 Common Pitfalls:
	•	Forgetting to log data changes.
	•	Unclear variable labels causing confusion.

⸻

📝 3. Code Style and Documentation

🔑 Key Principles:
	•	Comment liberally but meaningfully.
	•	Consistent indentation (2-4 spaces).
	•	Meaningful macro and variable names.
	•	Divide complex tasks into simpler blocks.
	•	Header templates for .do files.

📋 Example Header:

/******************************************************************
Purpose: Clean employment data
Inputs: raw/employment.dta
Outputs: processed/employment_clean.dta
Dependencies: None
Author: Your Name
Date: YYYY-MM-DD
*******************************************************************/

🖥️ Quick Start:

Clear environment at script start:

clear all
set more off

🚨 Common Pitfalls:
	•	Sparse or overly verbose comments.
	•	Ambiguous variable naming.

⸻

♻️ 4. Reproducibility Techniques

🔑 Key Principles:
	•	Always set seeds.
	•	Manage Stata version explicitly.
	•	Handle external dependencies clearly.
	•	Use master do-files for workflow.
	•	Log outputs systematically.

📋 Examples:

version 17.0
set seed 12345

🖥️ Quick Start:

Centralize log management:

log using "../output/logs/analysis.log", replace

🚨 Common Pitfalls:
	•	Ignoring seed-setting, leading to non-reproducible results.

⸻

⚙️ 5. Workflow Automation

🔑 Key Principles:
	•	Use loops for repetitive tasks.
	•	Write and reuse programs.
	•	Employ macros efficiently.
	•	Automate table/figure exports.

📋 Examples:

foreach var of varlist age income {
  summarize `var'
}

program define prep_data
  syntax using/
  import delimited "`using'", clear
end

🖥️ Quick Start:

Use loops immediately for repetitive summaries.

🚨 Common Pitfalls:
	•	Overcomplicating automation.
	•	Misuse of global macros.

⸻

✅ 6. Quality Assurance

🔑 Key Principles:
	•	Use assert statements for validation.
	•	Debug incrementally.
	•	Review code systematically.
	•	Implement basic unit tests.

📋 Examples:

assert age >= 0 & age <= 120

🖥️ Quick Start:

Immediately incorporate basic asserts after data cleaning.

🚨 Common Pitfalls:
	•	Ignoring data validation steps.

⸻

🤝 7. Collaboration and Sharing

🔑 Key Principles:
	•	Clean code before sharing.
	•	Provide clear README documentation.
	•	Follow data confidentiality protocols.
	•	Use version control (e.g., GitHub).

📋 Example README snippet:

# Project Title

Description of project, data, and scripts.

## Instructions to Reproduce:
- Run `code/master.do`

## Author(s)
- Your Name

🖥️ Quick Start:

Immediately create README.md in project root.

🚨 Common Pitfalls:
	•	Neglecting confidentiality concerns.

⸻

🗒️ One-Page Cheat Sheet:
	•	✅ Paths: Always relative, centrally defined.
	•	✅ Data: Raw immutable; audit trails essential.
	•	✅ Comments: Meaningful, clear, standardized.
	•	✅ Reproducibility: Set seeds; master .do files.
	•	✅ Automation: Loops and programs to streamline.
	•	✅ Quality: Frequent assert checks.
	•	✅ Sharing: Clear README; clean, documented code.

⸻

🖥️ Sample Workflow:

do "code/0_setup.do"
do "code/1_clean_data.do"
do "code/2_analysis.do"
do "code/3_output.do"


⸻

📚 Further Learning Resources:
	•	Stata Style Guide (World Bank)
	•	ado-lint: Linter for Stata
	•	UCLA IDRE Stata Resources
