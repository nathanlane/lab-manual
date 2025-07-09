# Coding Style Guide for Newcomers

This guide establishes basic conventions for all programming languages used in this lab. Following a consistent style improves readability, reduces cognitive overhead, and helps reviewers focus on the substance of your work.

## General Principles

1. **Be consistent** – adopt the style of this guide and existing project files.
2. **Favor clarity over cleverness** – prioritize understandable code.
3. **Document the rationale** – comments should explain *why* and reference relevant resources.
4. **Keep functions small** – each should do one thing well.
5. **Avoid duplication** – reuse functions and scripts where possible.

---

## Directory and File Naming

- Use lower-case and hyphens for directory names, e.g., `data-input`, `model-output`.
- Source code files use language conventions: `snake_case` for Python and R, `lowercase` for Stata.
- Prefix scripts that must run in sequence with two digits: `01_download.py`, `02_clean.do`.

---

## In-Line Comments

- Begin with a capital letter and end with a period when full sentences.
- Keep comments short but explanatory; avoid restating obvious code.
- Reference equations or papers when implementing specific methods.

---

## Commit Messages

1. Write in the present tense: "Add regression plot".
2. Limit to 50 characters on the summary line.
3. Link issues or tasks when relevant.

---

## Language Highlights

### Stata

- Use four spaces for indentation, no tabs.
- Align options and arguments vertically for long commands.
- Use `program define` with clear names such as `program define merge_industries`.

### R

- `tidyverse` style with `<-` for assignment.
- Load all libraries at the top of the script.
- Wrap lines at 80 characters.

### Python

- Follow [PEP 8](https://peps.python.org/pep-0008/).
- Run `black` to autoformat code and `flake8` for linting.
- Use type hints for all function arguments and return types.

---

Consistent style accelerates collaboration and reduces the back-and-forth during code reviews. When in doubt, mimic the style of existing scripts and ask a teammate for clarification.
