# Python Reproducibility Best Practices

This quick reference focuses on writing reproducible Python code for data analysis. It complements the main [Python Guide](../python.md).

## Environment Management

1. **Use Conda or `venv`** to isolate dependencies:
   ```bash
   conda create -n econ-env python=3.11
   conda activate econ-env
   ```
   Or, with `venv`:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
2. **Pin package versions** in `requirements.txt` or `environment.yml`.
3. **Capture the environment** with `pip freeze > requirements.txt` or `conda env export > environment.yml`.

## Project Structure

- Keep all notebooks in `notebooks/` and scripts in `code/`.
- Store data under `data/` with `input/`, `intermediate/`, and `output/` subfolders.
- Commit notebooks only after clearing outputs (`jupyter nbconvert --clear-output`).

## Using Pandas Effectively

1. **Explicit Types** – set dtypes when reading data to avoid surprises.
2. **Method Chaining** – prefer chaining with `.assign()` and `.pipe()` for readability.
3. **Saving Data** – write intermediate results to Parquet for speed and smaller files.
4. **Testing** – unit test critical functions that transform DataFrames using `pytest`.

## Example `environment.yml`

```yaml
name: econ-env
channels:
  - conda-forge
dependencies:
  - python=3.11
  - pandas
  - jupyterlab
  - numpy
  - pip
  - pip:
      - black
      - pytest
```

Create the environment:
```bash
conda env create -f environment.yml
conda activate econ-env
```

## Reproducible Workflows

- Run analyses via a single entry script (`python main.py`) or a Makefile.
- Log package versions and Python version in `output/logs/session_info.txt`:
  ```bash
  python -V > output/logs/session_info.txt
  pip list --format=freeze >> output/logs/session_info.txt
  ```
- Automate testing and formatting with pre-commit hooks.

Following these practices helps ensure that your results can be recreated months or years later with minimal friction.
