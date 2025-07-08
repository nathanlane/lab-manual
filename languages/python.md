# Python Guide

This guide follows [PEP 8](https://peps.python.org/pep-0008/) conventions and uses the **Black** formatter for consistent style.

---

## ⚙️ Environment Setup

1. **Create a virtual environment**
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```
3. **Format automatically**
   ```bash
   pip install black
   black .
   ```

Add `venv/` and `.mypy_cache/` to `.gitignore`.

---

## ✍️ Naming & Style Highlights

| Element | Convention | Example |
|---------|------------|---------|
| Variables & Functions | snake_case | `clean_data`, `run_regression` |
| Classes | PascalCase | `OLSModel` |
| Constants | UPPER_SNAKE_CASE | `MAX_ITERATIONS` |
| Docstrings | Triple quotes with [Google style](https://google.github.io/styleguide/pyguide.html#s3.8.3-comments-and-docstrings) | `"""Calculate Gini coefficient."""` |
| Type Hints | Use `typing` module | `def load_data(path: str) -> pd.DataFrame:` |

---

## 📝 File Header Template

```python
"""Short description (one sentence).

Author: Your Name <you@uni.edu>
Created: 2024-05-15
"""

from __future__ import annotations

import pandas as pd
```

---

## 🔑 Best Practices for Data Analysis

1. **Use `pathlib.Path`** – Avoid hard-coding OS-specific paths.
2. **Cache Intermediate Data** – Save cleaned datasets as `parquet` to speed up reruns.
3. **Prefer `pandas` Pipelines** – Chain operations with `assign` and `pipe`.
4. **Separate I/O & Logic** – Keep data loading functions separate from analysis logic.
5. **Logging** – Configure `logging` at `INFO` level in scripts; write to `output/logs/`.
6. **Unit Tests** – Place tests in `tests/` and run with `pytest`.

---

## 🏗️ Example Function with Type Hints & Docstring

```python
from pathlib import Path
import pandas as pd


def load_clean_data(path: Path) -> pd.DataFrame:
    """Load raw CSV, clean column names, and return a DataFrame.

    Args:
        path: Path to raw data CSV.

    Returns:
        Cleaned pandas DataFrame.
    """
    df = (
        pd.read_csv(path)
        .rename(columns=str.lower)
        .dropna(subset=["income", "wage"])
    )
    return df
```

---

## 🔍 Sample `pytest` Test

```python
import pandas as pd
from pathlib import Path
from your_project.cleaning import load_clean_data


def test_load_clean_data(tmp_path: Path):
    # Create a dummy CSV
    data = """income,wage\n1000,20\n2000,40"""
    test_file = tmp_path / "dummy.csv"
    test_file.write_text(data)

    df = load_clean_data(test_file)

    assert (df["income"] > 0).all()
    assert df.shape[0] == 2
```

Run all tests:
```bash
pytest -q
```

---

## 📚 Further Reading

- [Effective Pandas](https://github.com/TomAugspurger/effective-pandas)
- [Black formatter](https://black.readthedocs.io/en/stable/)