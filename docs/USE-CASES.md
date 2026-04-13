# MDSIV Use Cases

Detailed scenarios showing how MDSIV applies in real-world functional safety engineering.

---

## 1. Automotive Tier 1 Supplier Using an IEC 61508 Component

**Situation:** You're a safety engineer at an automotive Tier 1 supplier. You want to integrate a pressure sensor module that was developed and certified to IEC 61508 SIL 2. Your vehicle system requires ASIL B for the function that uses this sensor. Can you claim the sensor satisfies ASIL B?

**How to use MDSIV:**

1. Open the **Converter** tab
2. Set source to **IEC 61508**, target to **ISO 26262**
3. Set the IEC 61508 parameters that produced SIL 2 for this sensor (e.g., C2, F2, P1, W2 → score 4)
4. Click **Convert**

**What you'll see:**
- L1 (Risk): The risk addressed by SIL 2 maps to approximately ASIL B — the hazard levels are comparable
- L2 (Requirements): SIL 2's requirements map to approximately ASIL B as well (since IEC 61508 SIL 2 has SC=2, which aligns with ASIL B's SC)
- If there's a gap, the dimension bars show exactly where

**Engineering action:** If both layers agree, you have strong analytical support for claiming the SIL 2 component can satisfy ASIL B. Document the MDSIV analysis in your safety case as supporting evidence (not as the sole basis for the claim — you still need to show the component meets ISO 26262-specific requirements like PMHF allocation).

---

## 2. eVTOL Startup Navigating Automotive + Aviation

**Situation:** Your company is developing an electric vertical takeoff and landing vehicle. The flight control system uses automotive-grade ECUs (developed to ISO 26262 ASIL D) but must also meet DO-178C/DO-254 certification for aviation. Your certification consultant says "ASIL D is roughly DAL B" but your aviation assessor says "that's not enough for flight-critical systems, you need DAL A." Who's right?

**How to use MDSIV:**

1. Open the **Converter** tab
2. Set source to **ISO 26262**, target to **DO-178/254**
3. Set maximum risk parameters: S3, E4, C3 (ASIL D)
4. Click **Convert**
5. Expand **Show the Math**

**What you'll see:**
- L1 (Risk): ASIL D maps to DAL A — both address the highest risk band
- L2 (Requirements): ASIL D maps to approximately DAL B — because ISO 26262's systematic capability tops out around SC 3, while DAL A requires SC 4 equivalent
- Gap alert: The shortfall is in systematic capability and potentially PFH (DAL A requires 10⁻⁹/h vs. ASIL D's 10⁻⁸/h)

**Who's right?** Both are. The consultant is correct that ASIL D's *process requirements* align with DAL B. The assessor is correct that the *risk level* demands DAL A. MDSIV shows you exactly what supplementary measures you need: enhanced systematic processes (additional verification, formal methods) and a tighter hardware failure rate target.

---

## 3. Railway Signaling Company Entering Automotive

**Situation:** Your railway signaling division has decades of experience with CENELEC EN 50126/50128/50129 at SIL 4. The company is expanding into automotive ADAS. Management assumes "SIL 4 is the highest, so our processes exceed anything automotive requires." Is that true?

**How to use MDSIV:**

1. Open the **Risk Spectrum** tab
2. Click the **CENELEC SIL 4** segment
3. Observe where it maps across all other standards

**What you'll see:**
- CENELEC SIL 4 maps to ASIL D on Layer 1 (same risk band)
- CENELEC SIL 4 maps to ASIL D on Layer 2 as well (SIL 4 has SC=4, which exceeds ASIL D's SC=3)
- In this direction, there's actually no gap — railway SIL 4 *exceeds* ASIL D requirements

**But the real question is different.** Switch to the Converter, set source to ISO 26262 ASIL D, target CENELEC. You'll see that automotive's specific requirements (PMHF allocation methods, ISO 26262-specific work products, automotive-specific failure modes) are different from railway's. The *level* maps cleanly, but the *specific deliverables* differ. MDSIV tells you the integrity levels are equivalent; your gap analysis for process artifacts is a separate exercise.

---

## 4. Agricultural Autonomous Vehicle

**Situation:** You're developing an autonomous spraying system for agricultural tractors. The system falls under ISO 25119, but you want to apply ISO 26262 practices because your engineering team has automotive experience and better tool support for ISO 26262. Your HARA produces ASIL C. What AgPL should you target?

**How to use MDSIV:**

1. Open the **Converter** tab
2. Set source to **ISO 26262**, parameters for ASIL C (e.g., S3, E3, C3)
3. Set target to **ISO 25119**
4. Click **Convert**

**What you'll see:**
- The risk score and URS for ASIL C
- L1 mapping to the corresponding AgPL level
- L2 mapping showing whether your ISO 26262 processes satisfy that AgPL's requirements
- Dimension comparison showing where ISO 26262 ASIL C aligns or exceeds ISO 25119 requirements

**Alternatively**, use the agricultural parameters directly:

1. Set source to **ISO 25119** 
2. Input Se3 (fatal), Fr2 (frequent exposure), Av2 (not avoidable)
3. Target **ISO 26262**
4. See what ASIL level corresponds to your agricultural risk assessment

---

## 5. Multi-Domain Safety Case for a Sensor Supplier

**Situation:** You manufacture LIDAR sensors sold to automotive OEMs, railway integrators, and agricultural machinery companies. Rather than maintaining three separate safety certifications, you want to develop to a single baseline and document mappings to each domain standard.

**How to use MDSIV:**

1. Open the **Cross-Reference** tab
2. Filter to **IEC 61508** (the generic standard, logical baseline)
3. For your target SIL level (e.g., SIL 2), read across all target standards

**What you'll see:**
- SIL 2 → ASIL B (risk), ASIL B (requirements) — clean mapping
- SIL 2 → DAL C (risk), DAL C (requirements) — clean mapping  
- SIL 2 → CENELEC SIL 2 (trivially identical framework)
- SIL 2 → AgPL c (risk), AgPL c (requirements) — clean mapping

**Strategy:** Develop to IEC 61508 SIL 2, then document MDSIV-backed mappings to each domain standard. Your safety manual can reference the specific L1/L2 analysis for each customer's standard. Where gaps exist, document them as "application-specific requirements to be addressed by the integrator."

---

## 6. Auditor Reviewing a Cross-Domain Claim

**Situation:** You're assessing a safety case that claims "our IEC 61508 SIL 3 certified component satisfies ISO 26262 ASIL D requirements." You need to evaluate whether this claim is defensible.

**How to use MDSIV:**

1. Open the **Converter** tab
2. Set source **IEC 61508** with parameters producing SIL 3
3. Target **ISO 26262**
4. Click **Convert** and expand **Show the Math**

**What you'll see:**
- L1: SIL 3 addresses approximately ASIL C-level risk, not ASIL D
- L2: SIL 3's requirements (SC=3, PFH=10⁻⁷) align with approximately ASIL C-D
- The claim that SIL 3 = ASIL D is not supported on the risk equivalence dimension

**Assessment:** The claim overreaches. SIL 3 supports an ASIL C claim, not ASIL D. For ASIL D, the supplier would need SIL 4 (or SIL 3 with documented additional measures to close the gap on the hardware failure rate dimension, since ASIL D requires 10⁻⁸/h).

---

## 7. Standards Committee Cross-Reference Work

**Situation:** You're participating in a working group drafting an informative annex on cross-standard relationships. The group needs a systematic, transparent methodology rather than ad-hoc expert opinion.

**How to use MDSIV:**

1. Open the **Risk Spectrum** tab to visualize structural alignment across all five standards
2. Use the **Cross-Reference** tab for the complete mapping table
3. Reference **docs/ALGORITHM.md** for the formal specification
4. Use the **Converter** with specific parameter combinations to test edge cases

**What MDSIV provides:**
- A fully deterministic, reproducible methodology (same inputs always give same outputs)
- Transparent calibration (every URS band and requirement dimension is documented)
- Both valid perspectives (risk equivalence and requirements equivalence) presented simultaneously
- A clear articulation of *why* experts disagree, grounded in dimensional analysis rather than opinion

---

## 8. Academic Research on Safety Standard Harmonization

**Situation:** You're writing a paper on functional safety standard harmonization and need a formal framework for comparing integrity levels.

**What to cite:**
- The **additive threshold discovery**: ASIL determination is a pure additive function threshold(S+E+C), verified exhaustively
- The **multi-dimensional decomposition**: safety levels encode HW failure rate, systematic capability, and architecture independently
- The **two-layer mapping**: formalizes the risk-vs-requirements debate into two computable layers
- The **gap theorem**: the disagreement between experts is always 1 SIL level on the systematic capability dimension (for ISO 26262 → IEC 61508)

See the [algorithm specification](ALGORITHM.md) for formal definitions and the citation format in the README.
