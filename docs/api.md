# API Reference

`amortization-schedule` exposes four free functions. All take and return `f64`
(and one `u32` term). No structs, no traits to implement, no features to
enable.

## `monthly_payment`

```rust
pub fn monthly_payment(principal: f64, annual_rate: f64, months: u32) -> f64
```

Monthly payment on a **fully-amortizing fixed-rate loan** (the standard US
mortgage amortization formula).

- `annual_rate` is the nominal annual rate (e.g. `0.07` for 7%); divided by
  12 internally.
- `months` is the loan term in months (`360` = 30 years).
- Returns `0.0` when `months == 0`.
- When `annual_rate == 0.0`, returns the straight-line `principal / months`.

$$
M = P \cdot \frac{r(1+r)^n}{(1+r)^n - 1}
\quad\text{where}\quad
r = \frac{\text{annual rate}}{12},\;\; n = \text{months}
$$

```rust
use amortization_schedule::monthly_payment;

// $100k, 7%, 30y → ≈ $665.30/mo
let m = monthly_payment(100_000.0, 0.07, 360);
assert!((m - 665.30).abs() < 0.1);

// 0% interest → straight-line
assert!((monthly_payment(12_000.0, 0.0, 12) - 1_000.0).abs() < 1e-9);
```

## `period`

```rust
pub fn period(balance_before: f64, annual_rate: f64, payment: f64) -> (f64, f64, f64)
```

A single amortization period. Returns
`(interest, principal_paid, remaining_balance_after)`.

- `interest = balance_before * (annual_rate / 12)`.
- `principal_paid = (payment - interest)`, clamped so the final payment never
  overpays the balance.
- `remaining_balance` is floored at `0.0`.

```rust
use amortization_schedule::{monthly_payment, period};

let p = monthly_payment(100_000.0, 0.07, 360); // ≈ 665.30
let (interest, principal_paid, remaining) = period(100_000.0, 0.07, p);
// interest ≈ 583.33, principal_paid ≈ 81.97, remaining ≈ 99,918.03
```

## `total_interest`

```rust
pub fn total_interest(principal: f64, annual_rate: f64, months: u32) -> f64
```

Total interest paid over the full life of the loan:
`(monthly_payment * months) - principal`.

```rust
use amortization_schedule::total_interest;

let ti = total_interest(100_000.0, 0.07, 360); // ≈ 139,508.89
assert!(ti > 130_000.0 && ti < 140_000.0);
```

## `schedule`

```rust
pub fn schedule(principal: f64, annual_rate: f64, months: u32) -> Vec<(f64, f64, f64)>
```

The full amortization schedule as a `Vec` of `(interest, principal_paid,
remaining_balance)` tuples — one entry per period, in order, stopping once the
balance reaches zero.

```rust
use amortization_schedule::schedule;

let s = schedule(100_000.0, 0.07, 360);
assert_eq!(s.len(), 360);
assert!(s.last().unwrap().2 < 1.0); // final remaining balance ≈ 0
```

!!! note "Final period rounding"
    The last `principal_paid` is clamped to the remaining balance, so the
    schedule always closes exactly at `0.0` rather than overshooting by a few
    cents of float error. For a polished printable table with extra payments,
    use the [AssetLoanCalculator amortization tool](https://assetloancalculator.com/).
