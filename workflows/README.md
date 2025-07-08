# Workflows & Project Automation

This section shows how to glue together scripts, manage dependencies, and deliver fully reproducible research outputs.

---

## 🌀 Master Script Patterns (All Languages)

1. **Setup** – Install packages, set global paths.
2. **Data Prep** – Clean raw data into analytical datasets.
3. **Analysis** – Run models, simulations, or estimations.
4. **Output** – Export tables, figures, and logs.
5. **Session Info** – Record language version and package list.

Your master script (e.g., `master.do`, `run_all.R`, or `main.py`) should execute all the above steps **with a single command**.

---

## 📦 Dependency Management

| Language | Tool | How to Lock |
|----------|------|-------------|
| Stata | `ssctype pkg` | Document package versions in `docs/dependencies.md` |
| R | `renv` | Commit `renv.lock` |
| Python | `pip` + `requirements.txt` or `pipenv` | Pin versions and commit lockfile |
| Shell | System packages | Use `apt-get install` lines in setup script |

---

## 🔁 Automated Testing Approaches

- **Stata** – Use [`stataunit`](https://github.com/limoood/stataunit) for program tests.
- **R** – `testthat::test_dir("tests")` integrated with `devtools::check()`.
- **Python** – `pytest` with GitHub Actions.

See template CI workflows in `.github/workflows/` (coming soon).

---

## 💾 Creating Replication Packages with Shell Scripts

1. Run analysis and confirm logs are clean.
2. Collect *only* necessary data, scripts, and output.
3. Hash each file and generate a `manifest.json` for integrity.
4. Zip the package and test extraction in a clean environment.

See [shell replication template](../templates/shell_replication_template.md) for an example implementation.

---

## 🖥️ Documentation of Computational Environment

Always capture:

- OS and version (e.g., `uname -a`)
- Language interpreter versions (`stata -q`, `R --version`, `python --version`)
- Package versions (`ssc desc`, `renv::snapshot()`, `pip freeze`)

Include the output in `output/logs/session_info.txt`.

---

## 🐙 GitHub Essentials for Social Scientists

### Setup

1. Create a GitHub account and install Git.
2. Configure user details:
   ```bash
   git config --global user.name "Jane Doe"
   git config --global user.email "jane@uni.edu"
   ```
3. Generate an SSH key and add to GitHub.

### Basic Workflow

```bash
git clone <repo-url>
# work, work, work
git add file.R
git commit -m "Add data cleaning script"
git push origin main
git pull
```

### `.gitignore` Templates

```gitignore
# Stata
*.log
*.smcl

# R
.Rhistory
.RData
.Rproj.user

# Python
__pycache__/
*.pyc
venv/

# General
.DS_Store
*.tmp
```

Copy & paste the lines relevant to your project in the root `.gitignore`.

### Commit Messages

- Use **present tense**: "Add regression table".
- Limit to **50 characters**.
- Reference issues, e.g., `Fix wage variable (#12)`.

### Repository Structure for Replication

```
replication_package/
├── code/
├── data/
├── output/
├── README.md
└── LICENSE
```

---

Proceed to the [Templates](../templates/README.md) section to bootstrap your own scripts.