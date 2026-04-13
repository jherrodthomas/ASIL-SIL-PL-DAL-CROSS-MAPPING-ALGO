# MDSIV — Multi-Domain Safety Integrity Verification

A deterministic algorithm and interactive tool for mapping safety integrity levels across functional safety standards.

**ASIL** (ISO 26262) ↔ **SIL** (IEC 61508) ↔ **DAL** (DO-178C/DO-254) ↔ **SIL** (CENELEC EN 50126) ↔ **AgPL** (ISO 25119)

---

## The Problem

Functional safety engineers working across industries face a persistent problem: how does ASIL D in automotive relate to SIL 4 in industrial? Is DAL A in aviation equivalent to ASIL D? When a Tier 1 supplier's component is certified to SIL 3 under IEC 61508, what does that mean for an ISO 26262 safety case?

**Experts disagree.** Some say ASIL D = SIL 4. Others say ASIL D = SIL 3. Both cite legitimate technical reasoning. Published mapping tables from different sources contradict each other. Standards committees have deliberately avoided normative cross-references.

This project provides a mathematical explanation for *why* experts disagree, and a deterministic algorithm that produces *both* valid mappings simultaneously — with full transparency into which dimensions align and which don't.

## The Discovery

Safety integrity levels are not single numbers. They are **multi-dimensional constructs** collapsed into ordinal labels. Each level encodes requirements across at least three independent dimensions:

| Dimension | What it measures | IEC 61508 term | ISO 26262 term |
|-----------|-----------------|----------------|----------------|
| **D1** — Hardware failure rate | Tolerable probability of dangerous failure per hour | PFH | PMHF |
| **D2** — Systematic capability | Process rigor to prevent systematic faults | SC (Systematic Capability) | Software/process requirements |
| **D3** — Architecture | Structural measures against common-cause failure | HFT + SFF | SPFM + LFM |

When you compare ASIL D to SIL 4 across these dimensions, something remarkable happens:

| Dimension | ASIL D | SIL 4 equivalent | Match? |
|-----------|--------|-------------------|--------|
| D1 — HW failure rate | 10⁻⁸/h | SIL 4 | Yes |
| D2 — Systematic capability | ~SC 3 | SIL 3 | **No — 1 level gap** |
| D3 — Architecture | ~3 | SIL 3 | Partial |

**The gap is always exactly 1 SIL level, and it's always on the Systematic Capability dimension.** This is the root of the expert disagreement. Those who focus on risk (what failure rate does the level address?) get ASIL D = SIL 4. Those who focus on requirements (what process rigor does the level demand?) get ASIL D = SIL 3.

MDSIV doesn't pick a side. It computes both.

## The Algorithm

MDSIV uses a two-layer deterministic mapping:

### Layer 0: Additive Risk Score

A computationally verified discovery: **every major safety standard's risk assessment is a pure additive threshold function** over ordinal input parameters.

For ISO 26262:
```
ASIL(S, E, C) = threshold(S + E + C)

  QM   when  S + E + C ≤ 6
  A    when  S + E + C = 7
  B    when  S + E + C = 8
  C    when  S + E + C = 9
  D    when  S + E + C = 10
```

This was verified exhaustively against all 36 cells of the ISO 26262-3 HARA determination table. Every combination of Severity × Exposure × Controllability that sums to the same value produces the same ASIL. No exceptions.

The same additive property holds for IEC 61508 (C+F+P+W), CENELEC (Sev+Freq), ISO 25119 (Se+Fr+Av), and DO-178 (severity class).

### Layer 1: Risk Equivalence

Normalize each standard's additive score to a Universal Risk Score (URS) on [0, 1]:

```
URS = (score - score_min) / (score_max - score_min)
```

Map the URS to the target standard's threshold bands. This produces the "same risk" mapping: ASIL D → SIL 4, because both address the highest band of risk in their respective domains.

### Layer 2: Requirements Equivalence

Extract the source level's requirement vector across all three dimensions, compute equivalent SIL (eSIL) for each, and take the minimum:

```
eSIL_D1 = floor(-log10(PFH)) - 4     // HW failure rate
eSIL_D2 = clamp(SC, 0, 4)            // Systematic capability
eSIL_D3 = clamp(arch, 0, 4)          // Architecture

composite = min(eSIL_D1, eSIL_D2, eSIL_D3)
```

This produces the "same rigor" mapping: ASIL D → SIL 3, because the weakest dimension (SC ≈ 3) limits the equivalence.

### The Gap

When Layer 1 ≠ Layer 2, the algorithm reports a gap: the source level addresses a higher risk than its requirements can fully satisfy in the target framework. This gap is precisely the information a safety engineer needs to make an informed engineering judgment — and to identify which specific dimensions need supplementary measures.

## Quick Start

### Zero-Install (Recommended)

MDSIV is a single HTML file with zero dependencies. No build step, no npm, no framework.

```bash
git clone https://github.com/YOUR_USERNAME/mdsiv.git
open mdsiv/src/index.html
```

That's it. Open the file in any modern browser.

### GitHub Pages

To host it publicly:

1. Fork this repository
2. Go to Settings → Pages
3. Set source to `main` branch, `/src` folder
4. Your converter is live at `https://YOUR_USERNAME.github.io/mdsiv/`

### Embed in Your Documentation

The tool is a self-contained HTML file. Drop it into any documentation site, wiki, or intranet:

```html
<iframe src="path/to/index.html" width="100%" height="800" frameborder="0"></iframe>
```

## How to Use the Tool

See the full walkthrough in [docs/HOW-TO-GUIDE.md](docs/HOW-TO-GUIDE.md).

### Converter Tab

The converter works like a currency exchange for safety integrity levels:

1. **Select your source standard** from the left dropdown (e.g., ISO 26262)
2. **Set the risk input parameters** — these are the same parameters you'd determine during a Hazard Analysis and Risk Assessment (HARA):
   - For ISO 26262: Severity (S1–S3), Exposure (E1–E4), Controllability (C1–C3)
   - For IEC 61508: Consequence (C1–C4), Frequency (F1–F2), Avoidability (P1–P2), Demand Rate (W1–W3)
   - For DO-178: Failure Condition Severity (No Effect through Catastrophic)
   - For CENELEC: Severity (Insignificant–Catastrophic), Frequency (Incredible–Frequent)
   - For ISO 25119: Severity (Se0–Se3), Frequency (Fr1–Fr2), Avoidability (Av1–Av2)
3. **Select the target standard** from the right dropdown
4. **Click Convert**

The tool displays:
- **Source level** determined from your input parameters, with the computed risk score and URS
- **Layer 1 result** — the risk-equivalent level in the target standard
- **Layer 2 result** — the requirements-equivalent level (shown only when it differs)
- **Gap alert** — if the layers disagree, an explanation of which dimensions fall short
- **Show the Math** — expandable panel showing every computation step
- **Dimension bars** — visual comparison of source vs. target requirement levels across D1/D2/D3

### Cross-Reference Tab

A complete matrix of every source level mapped to every target standard. Filter by source standard using the pills at the top. Each row shows the Layer 1 mapping, Layer 2 mapping, and whether there's a gap.

### Risk Spectrum Tab

All five standards plotted on a shared Universal Risk Score axis (0 → 1). Each colored band is an interactive safety level. Click any segment to see:

- Its URS range
- The equivalent level in every other standard (Layer 1)
- Whether there's a gap in each mapping (Layer 2)

This view makes the structural relationships between standards immediately visible — you can see how ASIL D, SIL 4, DAL A, CENELEC SIL 4, and AgPL e all cluster at the high end of the risk spectrum, while their threshold boundaries don't align perfectly.

## Use Cases

See detailed scenarios in [docs/USE-CASES.md](docs/USE-CASES.md). Summary:

| Persona | Scenario | How MDSIV helps |
|---------|----------|-----------------|
| **Safety engineer** | Performing HARA for a multi-domain product | Input your risk parameters, get the equivalent level in every target standard instantly |
| **Tier 1 supplier** | Component certified to IEC 61508 SIL 2, need to claim ISO 26262 compliance | See exactly which ASIL level it maps to and whether there are gap dimensions to address |
| **Program manager** | Comparing certification costs across automotive vs. aviation | Use the matrix view to understand where standards align and where additional work is needed |
| **Auditor / assessor** | Reviewing a safety case that claims cross-standard equivalence | Verify the claim against both Layer 1 and Layer 2, identify any unstated assumptions |
| **eVTOL / flying car startup** | Navigating both ISO 26262 (automotive) and DO-178C (aviation) | Map ASIL levels to DAL levels, understand where automotive rigor falls short of aviation requirements |
| **Agricultural autonomy** | Applying ISO 26262 practices to ISO 25119 products | See how ASIL maps to AgPL with full transparency on gap dimensions |
| **Standards committee member** | Evaluating cross-references between standards | Use the formal algorithm as a basis for normative mapping proposals |
| **Academic researcher** | Studying functional safety harmonization | The additive threshold discovery and URS normalization are independently verifiable results |

## Standards Covered

| Standard | Domain | Levels | Risk Parameters |
|----------|--------|--------|-----------------|
| **ISO 26262** | Automotive | QM, ASIL A–D | Severity, Exposure, Controllability |
| **IEC 61508** | Industrial (generic) | SIL 1–4 | Consequence, Frequency, Avoidability, Demand Rate |
| **DO-178C / DO-254** | Aviation | DAL A–E | Failure Condition Severity |
| **CENELEC EN 50126** | Railway | SIL 1–4 | Consequence Severity, Frequency of Occurrence |
| **ISO 25119** | Agriculture | QM, AgPL a–e | Severity, Frequency, Avoidability |

## Algorithm Specification

See the formal specification in [docs/ALGORITHM.md](docs/ALGORITHM.md), including:

- Complete data tables for all five standards
- URS threshold band definitions and calibration rationale
- Requirement dimension extraction for each standard
- Pseudocode for the full pipeline
- Verification results (36/36 ASIL formula, all cross-domain L1/L2 mappings)

## Repository Structure

```
mdsiv/
├── README.md                 # This file
├── LICENSE                   # Apache 2.0
├── CONTRIBUTING.md           # How to contribute
├── CODE_OF_CONDUCT.md        # Community standards
├── src/
│   └── index.html            # The complete tool (single file, zero dependencies)
├── docs/
│   ├── ALGORITHM.md          # Formal algorithm specification
│   ├── HOW-TO-GUIDE.md       # Step-by-step usage walkthrough
│   └── USE-CASES.md          # Detailed use case scenarios
└── assets/                   # Screenshots, diagrams (for README)
```

## Limitations and Caveats

**This tool is an engineering analysis aid, not a substitute for formal safety assessment.**

Specific limitations to be aware of:

1. **URS band calibration.** The Universal Risk Score threshold bands are analytically derived to produce sensible mappings, but they are not defined by any single normative source. Different calibrations would produce different boundary mappings. The algorithm is transparent about exactly which bands it uses so you can evaluate them.

2. **Ordinal parameter compression.** Mapping ordinal risk parameters (e.g., S1/S2/S3) to integer values (1/2/3) assumes equal spacing between levels. The actual risk difference between S2 and S3 may be larger than between S1 and S2 in practice.

3. **Standard-specific nuance.** Each standard has context-specific provisions (ASIL decomposition, proven-in-use arguments, route-prove-route for railway) that affect real-world application but are not captured in a cross-domain mapping algorithm.

4. **Architectural metrics.** The D3 (architecture) dimension uses a simplified scalar that abstracts over quite different architectural approaches (HFT/SFF in IEC 61508 vs. SPFM/LFM in ISO 26262).

5. **DO-178C simplification.** Aviation uses a single-parameter risk assessment (failure condition severity), which means its URS normalization is coarser than multi-parameter standards.

6. **Not a legal or certification claim.** A mapping produced by this tool does not constitute evidence of compliance with any standard. It provides analytical transparency for engineering decision-making.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. Areas where contributions are especially welcome:

- **Additional standards**: ISO 13849 (machinery PL), IEC 62443 (industrial cybersecurity), MIL-STD-882 (defense)
- **URS calibration refinement**: Alternative band definitions with empirical justification
- **Validation against published mappings**: Comparing MDSIV output to mappings in published IEC TR or ISO TR documents
- **Localization**: Translating parameter labels and descriptions
- **Accessibility**: Improving screen reader support and keyboard navigation
- **Academic citations**: If you use this in research, please open an issue so we can track it

## Citation

If you use MDSIV in academic work, please cite:

```bibtex
@software{mdsiv2026,
  title  = {MDSIV: Multi-Domain Safety Integrity Verification},
  author = {Herrod, J.},
  year   = {2026},
  url    = {https://github.com/YOUR_USERNAME/mdsiv},
  note   = {Deterministic two-layer algorithm for cross-standard
            safety integrity level mapping}
}
```

## License

Apache 2.0 — see [LICENSE](LICENSE). The Apache license provides patent protection, which matters if this algorithm is referenced in standards work or implemented in commercial tools.

## Acknowledgments

This project builds on the structural analysis of five international safety standards. The standards themselves are copyrighted by their respective bodies (ISO, IEC, SAE, CENELEC). MDSIV does not reproduce copyrighted standard text — it implements an independent analytical algorithm based on publicly available structural information about how each standard's risk assessment and safety integrity levels are organized.
