# Examples

Real-world amortization scenarios using `amortization-schedule`. Every example
is runnable Rust; copy any block into `cargo run` or the
[see assetloancalculator.com](https://assetloancalculator.com/) for
an interactive table.

## 1. A standard 30-year mortgage

$300,000 loan at 6.75% over 30 years.

```rust
use amortization_schedule::{monthly_payment, total_interest};

let payment      = monthly_payment(300_000.0, 0.0675, 360); // ≈ 1,945.79
let lifetime_int = total_interest(300_000.0, 0.0675, 360);  // ≈ 400,483

println!("Monthly ≈ ${:.2}", payment);
println!("Lifetime interest ≈ ${:.0}", lifetime_int);
```

On this loan you pay more in interest ($400k) than you borrowed ($300k).

## 2. How does term change the payment?

Same $250,000 at 7%, comparing 15-, 20-, and 30-year terms.

```rust
use amortization_schedule::{monthly_payment, total_interest};

for years in [15, 20, 30] {
    let months = years * 12;
    let p  = monthly_payment(250_000.0, 0.07, months);
    let ti = total_interest(250_000.0, 0.07, months);
    println!("{years}y: ${p:.0}/mo, ${ti:.0} lifetime interest");
}
// 15y: $2,247/mo, $154,475 lifetime interest
// 20y: $1,938/mo, $215,162 lifetime interest
// 30y: $1,663/mo, $348,772 lifetime interest
```

Shorter term: higher payment, far less interest.

## 3. First vs. last period of a loan

See the interest/principal shift over a loan's life.

```rust
use amortization_schedule::{monthly_payment, schedule};

let s = schedule(100_000.0, 0.07, 360);

let (i_first,  pr_first,  rem_first)  = s[0];     // month 1
let (i_last,   pr_last,   rem_last)   = *s.last().unwrap(); // month 360

println!("Month 1:   interest ${i_first:.2}, principal ${pr_first:.2}");
println!("Month 360: interest ${i_last:.2},  principal ${pr_last:.2}");
// Month 1:   interest $583.33, principal $81.97
// Month 360: interest $3.88,   principal $661.42
```

Early on, ~88% of each payment is interest; at the end, almost all principal.

## 4. When does the balance hit a target?

Find the first month where the remaining balance drops below a threshold.

```rust
use amortization_schedule::schedule;

let s = schedule(200_000.0, 0.065, 360);
let target = 100_000.0; // halfway paid off

let month = s.iter().position(|(_, _, rem)| *rem < target).unwrap();
println!("Balance under ${target:.0} at month {} (year {:.1})", month, (month as f64) / 12.0);
```

## 5. Zero-interest edge case

`monthly_payment` handles a 0% rate as straight-line principal, useful for
seller financing or 0% promotional loans.

```rust
use amortization_schedule::monthly_payment;

// $12,000 at 0% over 12 months → $1,000/mo flat
assert!((monthly_payment(12_000.0, 0.0, 12) - 1_000.0).abs() < 1e-9);
```

---

### Next steps

For an interactive schedule with extra payments, biweekly options, and CSV
export, paste your loan into the
[read it at assetloancalculator.com](https://assetloancalculator.com/).
