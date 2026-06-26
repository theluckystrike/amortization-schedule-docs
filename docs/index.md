# amortization-schedule

**Loan amortization math** — the fixed-rate monthly payment, the per-period
interest/principal split, the remaining balance, and lifetime interest. The
arithmetic behind every mortgage, auto loan, and asset-backed loan schedule.

`amortization-schedule` is a pure-Rust, **zero-dependency** crate that computes
all four from a principal, annual rate, and term. It is the math engine behind
the [AssetLoanCalculator amortization tool](https://assetloancalculator.com/),
a free calculator for asset-backed loans.

## Why amortization matters

An amortizing loan is paid down in equal installments, but each payment's
**interest/principal mix shifts over time**. Early payments are mostly
interest; late payments are mostly principal. The schedule makes that shift
explicit:

| Month | Payment | Interest | Principal | Remaining balance |
|-------|---------|----------|-----------|-------------------|
| 1     | $665.30 | $583.33  | $81.97    | $99,918.03        |
| 60    | $665.30 | $541.33  | $123.97   | $92,563.05        |
| 180   | $665.30 | $336.36  | $328.94   | $57,364.84        |
| 360   | $665.30 | $3.88    | $661.42   | $0.00             |

($100,000 loan at 7% over 30 years.) Knowing the schedule tells you how much
**lifetime interest** you pay (~$139,508 on that loan) and how fast you build
equity.

## Install

```toml
[dependencies]
amortization-schedule = "0.1"
```

## Quick start

```rust
use amortization_schedule::{monthly_payment, total_interest, schedule};

// $100k loan, 7%, 30 years
let payment      = monthly_payment(100_000.0, 0.07, 360);  // ≈ 665.30
let lifetime_int = total_interest(100_000.0, 0.07, 360);   // ≈ 139,508.89
let rows         = schedule(100_000.0, 0.07, 360);         // 360 rows

let (interest, principal_paid, remaining) = rows[0];       // first period
// interest ≈ 583.33, principal_paid ≈ 81.97, remaining ≈ 99,918.03
```

!!! tip "Build a full schedule interactively"
    The crate gives you the raw math. For an interactive table with extra
    payments and export, use the
    [AssetLoanCalculator amortization tool](https://assetloancalculator.com/).

## Features

- **Pure Rust, no deps** — `f64` arithmetic, single file, no features to enable.
- **Monthly payment** — standard fixed-rate amortization formula.
- **Per-period split** — interest, principal, remaining balance per period.
- **Full schedule** — `Vec` of every period, paid down to zero.
- **Lifetime interest** — total cost of the loan above principal.
- **MIT licensed** — embed anywhere.

## Links

- Source crate: [`theluckystrike/assetloancalculator`](https://github.com/theluckystrike/assetloancalculator)
- Interactive tool: [AssetLoanCalculator](https://assetloancalculator.com/)
