# amortization-schedule-docs

MkDocs Material documentation site for the [`amortization-schedule`](https://github.com/theluckystrike/assetloancalculator)
Rust crate — loan amortization math: monthly payment, per-period split, remaining
balance, and lifetime interest.

**Live docs:** <https://theluckystrike.github.io/amortization-schedule-docs/>

The crate is the math engine behind the
[AssetLoanCalculator amortization tool](https://assetloancalculator.com/).

## Build locally

```bash
pip install -r requirements.txt
mkdocs serve
# → http://127.0.0.1:8000
```

## Publish

The site is built and deployed automatically by GitHub Actions
(`.github/workflows/ci.yml`) on every push to `main`. GitHub Pages serves the
build artifact directly (Pages build type = workflow).
