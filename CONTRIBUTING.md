# Contributing to MDSIV

Thank you for your interest in contributing to MDSIV. This project aims to bring mathematical transparency to cross-standard safety integrity mapping, and contributions from the functional safety community are essential to making it better.

## How to Contribute

### Reporting Issues

If you find a bug, an incorrect mapping, or have a suggestion, please open a GitHub issue with:

- A clear description of the problem or suggestion
- Steps to reproduce (for bugs)
- The expected vs. actual behavior
- Your reasoning (for mapping disagreements — cite specific standard clauses if possible)

### Submitting Changes

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/add-iso-13849`)
3. Make your changes
4. Test your changes (see Testing below)
5. Commit with a clear message explaining *why*, not just *what*
6. Open a pull request

### What Makes a Good PR

- **One concern per PR.** Don't mix a new standard with a URS band recalibration.
- **Explain your reasoning.** Safety-critical work demands transparency. If you're changing a threshold band, explain the rationale with references to specific standard clauses or published technical reports.
- **Maintain the single-file architecture.** The tool is intentionally a single HTML file with zero dependencies. This is a feature, not a limitation — it ensures the tool can be used in any environment, including air-gapped networks common in safety-critical industries.

## Areas Where Contributions Are Welcome

### High Priority

- **Additional standards:** ISO 13849 (machinery Performance Levels), IEC 62443 (industrial cybersecurity security levels), MIL-STD-882E (defense), IEC 60601 (medical devices)
- **Validation against published mappings:** If you have access to IEC TR, ISO TR, or SAE documents that include cross-standard mappings, comparing MDSIV output to those published mappings would be valuable
- **URS band calibration:** Alternative band definitions with empirical or analytical justification

### Medium Priority

- **Accessibility:** Improving keyboard navigation, screen reader support, ARIA labels, and color contrast for users with vision differences
- **Localization:** Translating parameter labels, descriptions, and UI text into other languages (German, Japanese, Chinese, and Korean are especially relevant for automotive safety)
- **Mobile responsiveness:** The tool works on mobile but could be better optimized

### Always Welcome

- **Bug fixes** with clear reproduction steps
- **Documentation improvements** — clearer explanations, additional examples, better diagrams
- **Academic citations** — if you use MDSIV in published research, please open an issue so we can track it and link to your work

## Adding a New Standard

To add a new standard to MDSIV, you need to provide:

1. **Risk parameters:** The ordinal input parameters used in the standard's risk assessment, with their discrete values mapped to integers starting from 0

2. **Additive formula:** Verification that the standard's risk assessment can be modeled as an additive threshold function over those parameters. This is the critical step — you need to show that the risk level is determined by the sum of parameters, not by specific combinations. Provide a verification table showing all input combinations.

3. **Score range:** The minimum and maximum possible additive scores

4. **Threshold bands:** For each safety level:
   - URS band [min, max] with rationale for the chosen boundaries
   - PFH target (or null if the level doesn't specify one)
   - Systematic capability score (mapped to 0–4 scale)
   - Architecture score (mapped to 0–4 scale)

5. **Validation:** At minimum, verify that the additive formula reproduces the standard's published risk assessment table, and that the L1/L2 mappings to at least one other standard produce reasonable results

Add the data to the `STDS` object in `src/index.html` following the existing pattern, and update `COLORS` with appropriate accent colors.

## Adding a New Standard (Template)

```javascript
"Standard Name": {
  short: "ABBR", scoreRange: [MIN, MAX], formula: "P1 + P2 + ...",
  params: [
    { id: "P1", name: "Parameter Name", values: [
      { label: "Value 1 — Description", val: 0 },
      { label: "Value 2 — Description", val: 1 },
      // ...
    ]},
    // ... more parameters
  ],
  thresholds: [
    { name: "Level Name", min: 0, max: 0.XX, pfh: null, sc: 0, arch: 0 },
    // ... more levels, URS bands must be contiguous and cover [0, 1]
  ]
}
```

## Testing

Since MDSIV is a single HTML file, testing is manual but should be thorough:

1. **Open `src/index.html` in a browser** — verify it loads without console errors
2. **Run each standard's converter** — set max-risk parameters, click Convert, verify the source level matches expectations
3. **Check the math panel** — expand "Show the Math" and verify each step
4. **Test edge cases** — minimum risk parameters (should produce QM or below-SIL), single-parameter standards (DO-178)
5. **Verify the cross-reference matrix** — spot-check several mappings against known published equivalences
6. **Test the risk spectrum** — click segments, verify highlighting behavior, check the detail card

For algorithmic changes, we recommend writing a standalone verification script (Node.js) that exhaustively tests the mapping function. See the verification scripts in the project history for examples.

## Code Style

- Vanilla JavaScript (no frameworks, no transpilation)
- Self-explanatory variable names, especially in the algorithm functions
- Comments for non-obvious decisions, especially URS band choices
- CSS custom properties for all colors, spacing, and typography

## Standards Copyright Notice

Safety standards are copyrighted by their respective standards bodies (ISO, IEC, SAE, CENELEC). MDSIV does not reproduce copyrighted text. When adding a new standard, describe parameter values and level names using your own words based on the publicly known structure of the standard. Do not copy tables, figures, or extended passages from any standard document.

## Communication

- **Issues:** For bugs, feature requests, and mapping discussions
- **Pull requests:** For code contributions
- **Discussions:** For broader questions about methodology, calibration philosophy, or use cases

We aim to respond to issues and PRs within one week. Complex algorithmic changes may take longer to review due to the safety-critical nature of the domain.
