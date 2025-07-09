# Stata Quick-Start: VS Code and Conda

This short guide helps Stata users get productive quickly using Visual Studio Code and Conda for managing dependencies.

## 1. Install Conda

1. Download [Miniconda](https://docs.conda.io/en/latest/miniconda.html) for your platform.
2. Run the installer and open a new terminal.
3. Create a base environment for Stata utilities:
   ```bash
   conda create -n stata-env python=3.11
   conda activate stata-env
   pip install pystata
   ```

## 2. Configure VS Code

1. Install the **Stata Enhanced** extension from the VS Code marketplace.
2. Open your project folder in VS Code.
3. Press <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and search for `Preferences: Open User Settings`.
4. Set the path to Stata in the `stataExecutable` field (e.g., `"C:\Program Files\Stata17\StataSE-64.exe"`).
5. Enable automatic log closing and `set more off` in your user snippet.

## 3. Create a Conda Environment for Tools

If you use Python for auxiliary tasks (e.g., data scraping), keep those packages separate:
```bash
conda create -n stata-tools pandas jupyterlab
conda activate stata-tools
```
Use this environment for Python scripts that interface with Stata or preprocess data.

## 4. Running Stata from VS Code

- Open a `.do` file and press <kbd>Ctrl</kbd>+<kbd>Enter</kbd> to execute the current line or selection.
- The *Stata Enhanced* extension sends code to your Stata instance and shows output in VS Code.
- Keep `master.do` at the project root so a new RA can run `do master.do` without navigating folders.

## 5. Helpful Extensions

- **GitLens** – Visualize Git history within VS Code.
- **Markdown All in One** – Write project documentation easily.
- **Python** – If you combine Stata with Python, this extension improves editing.

With VS Code and Conda configured, you can manage Stata projects alongside Python utilities in a single, reproducible workflow.
