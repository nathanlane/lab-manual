# Best Practices Checklist ✅

Use this checklist **before starting**, **before running analysis**, **before sharing code**, and **before publication**.

---

## 🟢 Before Starting a Project

- [ ] Read the [Fundamentals](fundamentals/README.md).
- [ ] Create a clean repository with `.gitignore`.
- [ ] Sketch project directory tree.
- [ ] Gather raw data and store in `data/input/`.

## 🟡 Before Running Analysis

- [ ] Activate virtualenv / renv / set Stata paths.
- [ ] Ensure raw data untouched; create copies if needed.
- [ ] Run unit tests (`pytest`, `testthat`, `stataunit`).
- [ ] Confirm scripts have headers and inline comments.

## 🟠 Before Sharing Code

- [ ] Run formatter/linter (Black, styler, ado-lint).
- [ ] Clean logs and remove large derived files from Git.
- [ ] Update `README.md` with run instructions.
- [ ] Push branch and open Pull Request with clear message.

## 🔴 Before Publication

- [ ] Regenerate **all** results from scratch on fresh machine.
- [ ] Verify checksums of replication package.
- [ ] Double-check variable definitions against codebook.
- [ ] Archive replication package on Zenodo/OSF with DOI.
- [ ] Add citation for replication package in manuscript.

---

Pro tip: Automate these steps with CI/CD pipelines and badge your repository with build status and test coverage.