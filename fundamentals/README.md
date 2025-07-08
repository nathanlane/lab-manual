# Fundamentals of Reproducible Research

This section covers the language-agnostic principles that underpin every successful empirical economics project. Master these fundamentals before diving into any language-specific guide.

---

## 1️⃣ Principles of Reproducible Research

1. **Transparency** – Make every step of your analysis visible.
2. **Automation** – Replace manual steps with scripts and pipelines.
3. **Version Control** – Track changes in code *and* data.
4. **Documentation** – Explain *what* you did, *why*, and *how* to rerun it.
5. **Portability** – Ensure others can reproduce results on another machine.

> "An article about computational science is not the scholarship itself, it is merely *advertising* of the scholarship." – D. Donoho (1998)

---

## 2️⃣ Universal Coding Best Practices

| Principle | Description |
|-----------|-------------|
| DRY (Don't Repeat Yourself) | Factor out repeated logic into functions or programs. |
| Meaningful Names | Use descriptive variable, function, and file names (`clean_data.py` > `cd.py`). |
| Small Commits | Save work frequently with focused, atomic commits. |
| Lint Early & Often | Use language-specific linters/formatters (Black for Python, *ado-lint* for Stata, *styler* for R). |
| Defensive Programming | Check assumptions, validate inputs, and fail loudly. |

---

## 3️⃣ Recommended Project Directory Structure

```text
project/
├── code/
│   ├── 0_data_prep/
│   ├── 1_analysis/
│   └── 2_output/
├── data/
│   ├── input/
│   ├── intermediate_datasets/
│   └── output/
├── output/
│   ├── figures/
│   ├── tables/
│   └── logs/
├── docs/
└── internal/
```

**Why this structure?**

* Separate raw `data/input` from processed `data/output` to protect originals.
* Keep all scripts in `code/`, ordered with numeric prefixes so execution order is obvious.
* Store generated artefacts in `output/` so you can add it to `.gitignore` if files are large.
* Place manuscripts, memos, or slide decks in `docs/`.
* Use `internal/` for proprietary or confidential material that never goes to version control.

---

## 4️⃣ Data Management Principles

1. **Immutable Raw Data** – Never overwrite the original dataset. Copy it into `data/input/`.
2. **Single Source of Truth** – Keep only one processed version of each dataset.
3. **Document Transformations** – Record every cleaning, merge, or reshape step in code.
4. **Data Dictionaries** – Maintain variable definitions and units in `docs/codebook.md`.

---

## 5️⃣ Version Control Concepts

- **Repository** – A collection of tracked files.
- **Commit** – A snapshot of the repository at a point in time.
- **Branch** – A parallel line of development (use feature branches!).
- **Pull Request** – A proposal to merge changes into another branch.

> Use `git add -p` to stage *only* the lines that belong to the current logical change.

---

## 6️⃣ Documentation Standards

### Script Headers (minimum template)

```text
# ==============================================================================
# PURPOSE:        One-sentence summary of what the script does
# AUTHOR:         Your Name <you@uni.edu>
# CREATED:        2024-05-15
# LAST UPDATED:   2024-05-15
# INPUTS:         ../data/input/raw_survey.csv
# OUTPUTS:        ../data/output/clean_survey.csv
# NOTES:          Any caveats or dependencies
# ==============================================================================
```

### Inline Comments

- Explain *why*, not just *what* (code shows *what*).
- Use comments to cite papers or methods.

### README Files

Each major directory **must** contain a `README.md` with:

1. Purpose of the directory.
2. How to reproduce its contents.
3. Key files and their roles.

---

☑️ *Continue to the [Language-Specific Guides](../languages/README.md) once you are comfortable with these fundamentals.*