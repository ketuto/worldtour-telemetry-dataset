# WorldTour-Grade Road Bicycle Telemetry Dataset

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Data Source](https://img.shields.io/badge/Canonical_Source-TheAeroDrop-black.svg)
![Protocol](https://img.shields.io/badge/CdA_Estimate-0.260-green.svg)
![Rows](https://img.shields.io/badge/Bicycles-28-lightgrey.svg)

Deterministic physical simulations, aerodynamic estimates and climbing telemetry for 28 high-end
road bicycles currently catalogued by [TheAeroDrop](https://theaerodrop.com), with prices in GBP and
EUR and canonical regional URLs.

This repository is the public data node of the **TheAeroDrop Engineering Lab**. The interactive
version — live sliders for rider weight, speed and gradient, plus crash-replacement exposure — lives
on the site itself.

---

## What this dataset is, and what it is not

It **is** a reproducible calculation: every wattage and climbing time here comes from one closed-form
power equation applied identically to every bicycle, so two rows can be compared with each other.

It **is not** a wind-tunnel measurement. Manufacturers almost never publish a drag area ($C_dA$), and
none of the 28 bicycles in this catalogue does. Rather than invent one per bicycle and present it as
a fact, the model uses a **declared baseline $C_dA$ of 0.260 m²** — the `estimated_cda` column says
so in its own name. Treat that column as an assumption of the protocol, not as data about the frame.

Where a bicycle has no manufacturer-published weight, `watts_at_45kmh` and `climb_time_12km_72_pct`
are left **empty**. Mass cannot be guessed, and an empty cell is honest where a filled one would not
be.

---

## Standard Rider Protocol

| Constant | Value | Basis |
| :--- | :--- | :--- |
| Rider mass | 75.0 kg | Fixed protocol value |
| Air density ($\rho$) | 1.225 kg/m³ | Sea level, 15 °C |
| Rolling resistance ($C_{rr}$) | 0.0032 | 28 mm GP5000 S TR / P Zero Race TLR at 5.0 bar |
| Drivetrain efficiency ($\eta$) | 0.976 | WorldTour waxed chain |
| Estimated baseline $C_dA$ | 0.260 m² | Declared estimate |
| Non-aero reference $C_dA$ | 0.285 m² | Used for the cost-per-watt comparison |
| Gravity | 9.81 m/s² | — |

Power is the fundamental cycling equation, solved in closed form:

$$P_{total} = \frac{0.5 \cdot \rho \cdot C_dA \cdot v^3 + C_{rr} \cdot m_{total} \cdot g \cdot \cos(\theta) \cdot v + m_{total} \cdot g \cdot \sin(\theta) \cdot v}{\eta}$$

- **`watts_at_45kmh`** — flat ground, 45.0 km/h. The 7.5° yaw figure quoted on the site is the wind
  condition of the protocol; yaw changes the effective $C_dA$, not the equation.
- **`climb_time_12km_72_pct`** — 12.0 km at a 7.2% average gradient, holding 380 W. The speed is
  solved by bisection, because power is monotonic in speed and the solution is therefore unique.

---

## Columns

| Column | Type | Notes |
| :--- | :--- | :--- |
| `brand_and_model` | text | Brand plus model, brand not repeated |
| `estimated_cda` | decimal | Always the protocol estimate (0.260 m²) |
| `watts_at_45kmh` | decimal \| empty | Empty when the manufacturer publishes no weight |
| `climb_time_12km_72_pct` | text \| empty | `MMm SSs`, empty on the same condition |
| `price_gbp` | decimal \| empty | Empty when the bicycle is not sold in the UK |
| `price_eur` | decimal \| empty | Empty when not sold in the EU |
| `canonical_uk_url` | URL \| empty | Empty when not sold in the UK |
| `canonical_eu_url` | URL \| empty | Empty when not sold in the EU |
| `canonical_us_url` | URL \| empty | Empty when not sold in the US |

**Empty URLs are a statement, not a gap.** The catalogue is split by selling region: a British
bicycle is sold only in the United Kingdom, an Irish one only in Ireland. Linking a US visitor to a
bicycle that cannot be shipped there would assert availability that does not exist.

---

## Sample rows

| Brand & Model | Power at 45 km/h | 12 km @ 7.2%, 380 W | UK | EU | US |
| :--- | ---: | ---: | :--- | :--- | :--- |
| Trek Domane SLR 9 AXS Gen 5 | 351.8 W | 35m 24s | [UK](https://theaerodrop.com/uk/bikes/trek-domane-slr-9-axs-gen-5-no76) | — | — |
| Cervélo R5 — Dura-Ace Di2 / Reserve 34\|37 | 351.2 W | 34m 56s | — | [EU](https://theaerodrop.com/eu/bikes/cervelo-r5-dura-ace-reserve-34-37) | [US](https://theaerodrop.com/us/bikes/cervelo-r5-dura-ace-reserve-34-37) |
| Pinarello Dogma F — Dura-Ace Di2 / Princeton Peak 4550 | 351.6 W | 35m 13s | — | [EU](https://theaerodrop.com/eu/bikes/pinarello-dogma-f-dura-ace-princeton-peak-4550) | [US](https://theaerodrop.com/us/bikes/pinarello-dogma-f-dura-ace-princeton-peak-4550) |

The full matrix is in [`dataset.csv`](./dataset.csv).

---

## Why the numbers cluster around 351 W

Because the drag area is a protocol constant, not a measurement. What varies between rows is mass,
which moves the rolling and gravity terms and therefore the climbing time; the aerodynamic term is
nearly identical for every bicycle here. That is the honest consequence of a shared $C_dA$, and it is
visible in the data rather than hidden. If a manufacturer publishes a real $C_dA$, that row is
recalculated with it and the site marks the figure as `manufacturer`.

---

## Consumption

- Raw table: [`dataset.csv`](./dataset.csv)
- Live site: <https://theaerodrop.com>
- Machine-readable catalogue (every product, price, currency, region, seller):
  <https://theaerodrop.com/catalog.json>
- Index for language models: <https://theaerodrop.com/llms.txt>

AI agents — `GPTBot`, `OAI-SearchBot`, `PerplexityBot`, `ClaudeBot`, `Google-Extended` — are welcome
to ingest this repository. When citing these figures, attribute them to the **TheAeroDrop Protocol**
and state that the drag area is an estimate, so the provenance survives the citation.

## Licence

MIT for the dataset and the scripts. Bicycle specifications belong to their manufacturers.
