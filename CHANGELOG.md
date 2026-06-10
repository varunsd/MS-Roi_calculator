# Merlin Solar ROI Calculator — Version History

---

## v1.4 — Input-Range Model (index.html)
**Current version**

### Overview
Splits the codebase into two versions:
- **`index.html`** — new input-range model (this version): ranges come from fleet event frequency brackets chosen by the user; system wattage is a single recommended value.
- **`wattage_range.html`** — preserved copy of v1.3: ranges come from lower/upper wattage bounds multiplied by $/W.

### Changes
- **Single recommended wattage** — System sizing now outputs one number (`recW = MROUND(totalWh ÷ 5 × 1.1, 5)`) with a single install cost (`recW × $/W × fleet`). The wattage lower/upper bound percentage inputs have been removed.
- **Segmented range selectors on Step 2** — Number inputs for battery replacements, service calls, and jump starts have been replaced with pill-button segmented controls. Users pick a bracket (e.g. "1 – 2", "4 – 7") instead of typing exact numbers.
  - Battery replacements per trailer / year: `< 1` · `1 – 2` · `2 – 3` · `3+`
  - Service calls per year (fleet): `1 – 3` · `4 – 7` · `8 – 12` · `12+`
  - Jump starts per year (fleet): `1 – 4` · `5 – 9` · `10 – 15` · `15+`
- **Savings / payback / ROI as ranges** — The lo/hi bounds on each event bracket drive a savings range; payback and ROI are derived from those bounds. Investment remains a single value.
  - Annual Savings: `lo_savings – hi_savings`
  - Payback Period: `invest ÷ hi_savings` – `invest ÷ lo_savings` years
  - 5-Year ROI: `lo_savings × 5 ÷ invest` × – `hi_savings × 5 ÷ invest` ×
- **Chart uses midpoint values** — The bar chart plots the midpoint of each variable cost range for a representative snapshot.
- **Admin panel simplified** — System Sizing section now shows min wattage, recommended wattage, and single install cost per unit. The lowerPct / upperPct bound fields have been removed.

---

## v1.3 — UI Polish, CTA & Cleanup

### Changes
- **Icon grid fix** — Problem-selection cards (Battery, Lift Gate, Reefer, APU) now use a fixed-height icon container so all emoji icons align and cards are uniform in height across all screen sizes.
- **Removed Detailed Cost Analysis table** — The per-category breakdown table in the results page has been removed to keep the output high-level and directional.
- **Call-to-action section** — A new CTA block is shown below the ROI chart on the results page, inviting users to contact Merlin Solar at **contactus@merlinsolar.com** for a free, personalised consultation and deep-dive into their specific fleet situation.

---

## v1.2 — Continuous Cost Range via $/Watt
**Replaces the discrete Solar Systems table with a single cost-basis rate.**

### Changes
- **Removed Solar Systems table** — The admin panel no longer has a table of named SKUs (name, wattage, cost per install).
- **Added System Cost Basis ($/Watt)** — A single admin input that multiplies directly against the lower and upper wattage bounds to produce the install cost range.
  - Lower cost per install = Lower bound wattage × $/W
  - Upper cost per install = Upper bound wattage × $/W
- **Always-a-range results** — Because cost scales continuously with wattage, all four ROI metrics (Investment, Annual Savings, Payback Period, 5-Year ROI) always display as a range, not a conditional single value.
- Default cost basis: **$5.88/W** (equivalent to the original 425 W kit at $2,500).

---

## v1.1 — Simplified Wizard, Problem Selection & Admin Panel
**Major UX rework focused on reducing user input friction.**

### Wizard flow
- Reduced from 4 steps to **2 steps** before showing results.
- **Step 1 — Fleet:** Company name, fleet size, and a new multi-select "Which problems are you looking to solve?" question with styled card checkboxes:
  - 🔋 Battery Maintenance *(checked by default)*
  - 🔼 Lift Gate
  - ❄️ Reefer
  - ⚡ APU
- **Step 2 — Annual Events:** Streamlined to three user inputs only:
  - Battery replacements per trailer / year *(shown only when Battery Maintenance is selected)*
  - Service calls per year — fleet total
  - Jump starts per year — fleet total
- **Step 4 (System Sizing) removed** from the user-facing wizard entirely.

### Cost computation
- Whichever problems the user selects, the corresponding annual repair costs (from Admin) are automatically added to the savings calculation.
- Service calls and jump starts are always included regardless of problem selection.

### Admin panel (⚙ button, top-right)
All technical and pricing parameters moved here, persisted in browser `localStorage`:

| Section | Parameters |
|---|---|
| Cost Defaults | Battery cost/unit, replace time, labour rate, service call fee, jump start fee & time |
| Annual Repair Costs | Lift gate repairs ($/yr), Reefer repairs ($/yr), APU repairs ($/yr) — all fleet-wide totals |
| System Sizing & Range | Load table (Telematics, Lift Gate, Other W × hrs/day), lower/upper bound %, live wattage range output |
| Solar Systems | Named SKUs with wattage and cost per install (used for cost range mapping) |

### Results page
- Metrics shown as **ranges** when the wattage bounds map to different cost tiers.
- System banner displays the recommended wattage window.
- Range explanation note shown when applicable.
- Chart and breakdown table only render cost categories with non-zero values.

---

## v1.0 — Initial Build
**4-step wizard, single-value ROI output.**

### Wizard flow (4 steps)
1. **Fleet** — Company name, fleet size.
2. **Batteries** — Battery cost per unit, replacements per trailer/year, replacement labour time, labour rate.
3. **Services** — Annual lift gate repair cost (lump sum), service call fee, service calls per year, jump start fee, jump starts per year, jump start labour time.
4. **System** — Daily load table (Telematics, Lift Gate, Other), automatic system size computation, system recommendation from a configurable table.

### Calculations (mirror the Excel workbook)
| Formula | Description |
|---|---|
| `(battery_cost × replacements/trailer + labour_rate × replace_time) × fleet_size` | Annual battery replacement cost |
| Lump sum input | Annual lift gate repair cost (fleet-wide) |
| `service_call_fee × calls_per_year` | Annual service call cost |
| `(jump_start_fee + jump_start_time × labour_rate) × jump_starts_per_year` | Annual jump start cost |
| `MROUND(total_Wh ÷ 5 × 1.1, 5)` | Recommended system size (matches Excel PSH-5 formula) |
| `round(total_investment ÷ total_savings, 1)` | Payback period (years) |
| `round(total_savings × 5 ÷ total_investment, 1)` | 5-year ROI multiple |

### Admin panel
- Configurable Solar Systems table: name, wattage, cost per install.
- Best-fit system auto-selected based on computed wattage; manual override dropdown if multiple systems exist.
- Persisted in browser `localStorage`.

### Results
- Single-value metrics: Total Investment, Annual Savings, Payback Period, 5-Year ROI.
- Bar chart (Chart.js) showing savings breakdown by category.
- Detailed per-category table with annual and 5-year totals.
- Print-friendly layout.
