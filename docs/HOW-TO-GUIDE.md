# How to Use MDSIV

A step-by-step walkthrough of the Safety Integrity Converter.

---

## Getting Started

MDSIV is a single HTML file with no dependencies. Open `src/index.html` in any modern browser (Chrome, Firefox, Safari, Edge). No installation, no build step, no internet connection required after download.

The interface has three views, accessible from the left sidebar:

1. **Converter** — The main tool. Convert safety levels between standards.
2. **Cross-Reference** — Complete mapping tables for every standard pair.
3. **Risk Spectrum** — Visual overview of how all standards align on the risk scale.

---

## The Converter

This is the primary workflow. It works like a currency converter: pick a source, set parameters, pick a target, get the result.

### Step 1: Select Your Source Standard

Click the **From** dropdown on the left side. Choose the standard you're working in:

- **ISO 26262** — Automotive (ASIL)
- **IEC 61508** — Industrial generic (SIL)
- **DO-178/254** — Aviation (DAL)
- **CENELEC** — Railway (SIL)
- **ISO 25119** — Agriculture (AgPL)

The parameter inputs below will update to show the risk parameters for your chosen standard.

### Step 2: Set Your Risk Parameters

These correspond to the inputs you'd determine during a Hazard Analysis and Risk Assessment (HARA). Set each dropdown to match your specific hazard assessment:

**For ISO 26262:**
- **S (Severity):** How severe are the potential injuries? S1 = light, S3 = fatal
- **E (Exposure):** How often is the driver exposed to the hazardous situation? E1 = very rare, E4 = high probability
- **C (Controllability):** How easily can the driver avoid the harm? C1 = simply controllable, C3 = difficult/uncontrollable

**For IEC 61508:**
- **C (Consequence):** Severity of the hazardous event. C1 = minor injury, C4 = catastrophic
- **F (Frequency):** How often are people in the hazardous zone? F1 = rare, F2 = frequent
- **P (Avoidability):** Can people avoid the hazardous event? P1 = possible, P2 = almost impossible
- **W (Demand Rate):** How often is the safety function demanded? W1 = very low, W3 = high

**For DO-178/254:**
- **Severity:** Single parameter from No Effect to Catastrophic

**For CENELEC:**
- **Severity:** Insignificant to Catastrophic
- **Frequency:** Incredible to Frequent

**For ISO 25119:**
- **Se (Severity):** Se0 = no injury, Se3 = death
- **Fr (Frequency):** Fr1 = rare, Fr2 = frequent
- **Av (Avoidability):** Av1 = avoidable, Av2 = not easily avoidable

### Step 3: Select Your Target Standard

Click the **To** dropdown on the right side. Choose which standard you want to map to.

Use the **swap button** (circular arrows between the two sides) to reverse source and target instantly.

### Step 4: Click Convert

The tool computes and displays:

#### Source Result (left side)
A colored badge showing the determined safety level (e.g., "ASIL D"), plus the raw risk score and Universal Risk Score (URS).

#### Target Result (right side)
- **Layer 1 — Risk Equivalence:** The target level that addresses the same level of risk. This is the "naive" mapping most people think of.
- **Layer 2 — Requirements Equivalence:** The target level whose safety measures are equally rigorous. This appears only when it differs from Layer 1.

#### Layer Cards
Two summary cards explaining each layer's mapping in plain language.

#### Gap Alert
If the layers disagree, an amber alert explains the gap: which level addresses the risk, which level the requirements satisfy, and which specific dimensions fall short. If they agree, a green confirmation appears.

### Step 5: Examine the Math

Click **Show the Math** to expand a panel showing every computation step:

1. **Additive Risk Score** — The formula and parameter values summed
2. **Universal Risk Score** — Normalization to [0, 1]
3. **Source Level** — Which threshold band the URS falls in, plus the level's PFH, SC, and architecture values
4. **Layer 1** — URS mapped to the target standard's bands
5. **Layer 2** — Requirement vector extraction, eSIL computation for each dimension, min() composite
6. **Gap Analysis** — Whether the layers agree and why

### Step 6: Review Dimension Bars

Below the math panel, horizontal bars compare the source level's requirement dimensions against the target's Layer 1 level:

- **HW Failure Rate (D1):** PFH converted to eSIL scale (0–4)
- **Systematic Capability (D2):** SC on eSIL scale
- **Architecture (D3):** Architectural constraints on eSIL scale

Each bar shows source (solid) vs. target (transparent). If the source bar is shorter, that dimension is shown in amber — it's a shortfall that would need supplementary measures.

---

## The Cross-Reference Matrix

Switch to this view by clicking **Cross-Reference** in the sidebar.

### Filter by Source Standard

The pill buttons at the top filter the view. Click **All** to see every possible mapping, or click a specific standard (ASIL, SIL, DAL, AgPL) to focus on mappings from that standard.

### Reading the Tables

Each table shows:
- **Source Level** — The level being mapped from
- **L1 Risk** — Layer 1 (risk equivalence) result in the target standard
- **L2 Reqs** — Layer 2 (requirements equivalence) result
- **Gap** — Green checkmark if both layers agree, amber "GAP" tag if they disagree

### What to Look For

- **Clean mappings (checkmark):** Both layers agree. The mapping is straightforward.
- **Gap mappings:** The source level addresses a higher risk than its requirements satisfy in the target standard. This is useful information — it tells you where additional measures are needed.
- **Patterns:** Notice that gaps are systematic, not random. They consistently appear on the same dimension (systematic capability) and are always exactly 1 level.

---

## The Risk Spectrum

Switch to this view by clicking **Risk Spectrum** in the sidebar.

### What You're Looking At

Five horizontal bars, one per standard, plotted on a shared Universal Risk Score (URS) axis from 0 to 1. Each colored segment is a safety integrity level. Wider segments cover a broader URS range. Darker segments represent higher integrity levels.

### Interactive Features

**Hover** over any segment to see equivalent levels highlighted across all other standards. Non-matching segments dim.

**Click** a segment to lock the selection. A detail card appears below showing:
- The selected level's name and URS range
- Its equivalent level in every other standard (Layer 1 mapping)
- Whether each mapping has a gap (Layer 2 disagreement)

**Click the same segment again** to deselect.

### What This View Reveals

- **Structural alignment:** Standards with similar URS band widths are structurally similar (IEC 61508 and CENELEC share almost identical bands)
- **Granularity differences:** ISO 25119 has 6 levels (QM + AgPL a through e), while DO-178 has 5 (DAL E through A), and ISO 26262 has 5 (QM + ASIL A through D). More levels mean finer risk discrimination.
- **Boundary misalignment:** Even when two standards have "the same" number of levels, their URS boundaries don't align perfectly — this is a source of mapping ambiguity at the edges.

---

## Tips for Effective Use

1. **Start with the Converter** for a specific analysis. Use the Risk Spectrum for intuition-building.

2. **Always check both layers.** A Layer 1 mapping alone can be misleading — it tells you the risk is equivalent but not whether the requirements are sufficient.

3. **Pay attention to which dimension causes the gap.** If it's systematic capability, you may need enhanced software verification. If it's hardware failure rate, you need tighter PMHF allocation. If it's architecture, you need additional redundancy or diagnostic coverage.

4. **Use the math panel for your safety case.** The step-by-step computation provides auditable evidence of how the mapping was derived. Screenshot or reference the specific steps in your documentation.

5. **Cross-validate with the Cross-Reference tab.** After running a specific conversion, check the matrix to see how your result fits the broader pattern across all levels.

6. **Remember the limitations.** MDSIV is an analytical tool, not a certification artifact. It provides transparency and mathematical rigor for engineering judgment. The final safety assessment must consider standard-specific requirements, application context, and regulatory expectations.
