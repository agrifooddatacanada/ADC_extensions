**Author:** Ali Asjad  
**Date published:** 21/05/2026 
**Version:** 1.0.0

# Overlay Creation Process (ADC) 

## Step 1: Draft Creation

The authoritative structure for overlay draft documentation is maintained by the OCA Package project and may change over time. Always follow the official requirements:

- OCA Package Overlay Documentation Requirements: [GitHub (latest)](https://github.com/agrifooddatacanada/OCA_package_standard?tab=readme-ov-file#oca-package-overlay-documentation-requirements)

### Purpose
Provide a repeatable, future-proof process for authoring a new overlay draft that conforms to the official structure and canonicalization expectations.

### Where to place the draft
- Create a new Markdown file under `draft/` named after your overlay, e.g. `draft/<overlay_name>.md`.
- Use a descriptive file name aligned with the overlay’s `type` (e.g., `file_delimiter.md`, `array_delimiter.md`).

### Required sections and structure
Use the exact headings and ordering from the official OCA Package Overlay Documentation Requirements (link above). Do not rely on cached templates, as the spec can evolve.

Include, at minimum:
- Title, Community grouping, Authors, Date released (use “Intentionally left empty” if unknown)
- Description (purpose, problem addressed, defined keys with types and constraints)
- Canonicalization Rules (key order and when arrays are insertion-ordered vs. objects lexicographically ordered; reference RFC 8785 §3.2.3)
- Example (canonicalized overlay JSON; SAID values may be illustrative; you may format after SAID calculation)
- Rules summary (MUST/SHOULD/MAY, required vs optional keys, accepted value types/ranges)
- Test case (fully canonicalized `oca_package` JSON with SAIDs, minimal dependencies, not reformatted in a way that breaks SAID reproducibility)
- Normative references (OCA spec v1.0.1, RFC 8785 §3.2.3, CESR, OCA Package Standard)

### Canonicalization notes
- Follow [RFC 8785 Section 3.2.3] for object property sorting where required.
- Arrays are insertion ordered unless specified otherwise.
- SAID calculation requires fully compact JSON without extraneous whitespace prior to computation.

### Writing tips
- Be explicit about defaults and constraints for each key (e.g., value types, allowed ranges, internationalization considerations).
- Clearly state interactions or boundaries with other overlays (e.g., file delimiter vs. decimal separator).
- Keep the Test case strictly canonical for reproducible SAID calculation.

### Quality checklist (for the draft)
- [ ] File created in `draft/` with a descriptive name
- [ ] All required sections present per the official link above
- [ ] Canonicalization rules documented and consistent with examples
- [ ] Example overlay JSON canonicalized and consistent
- [ ] Test case is a fully canonicalized `oca_package` JSON with SAIDs
- [ ] Rules summary lists all keys with required/optional status, types, and constraints
- [ ] Normative references included

### Reference
- OCA Package Overlay Documentation Requirements: [GitHub (latest)](https://github.com/agrifooddatacanada/OCA_package_standard?tab=readme-ov-file#oca-package-overlay-documentation-requirements)

## Step 2: UI Design

### Purpose
Create an initial visual concept for how users will interact with the overlay’s functionality (e.g., data entry/export settings, validation feedback, localization controls). This informs feasibility, usability, and downstream implementation.

### Tools (choose any)
- Figma, Adobe Photoshop, Canva (or an equivalent design tool available to you)
- Prefer Figma when collaborative review and clickable prototypes are helpful

### What to design
- Core screens and states needed to configure and use the overlay
- Key interactions: add/edit/remove items, validation messages, error states
- Edge cases: long labels, small screens, disabled/readonly states (if relevant)

### Design conventions
- Use ADC design patterns where applicable (spacing, typography, controls)
- Ensure sufficient color contrast and accessible touch/click targets
- Make field labels, helper text, and error text concise and consistent

### Deliverables
- A single PowerPoint file containing:
  - Exported design screens (PNG/JPEG) with clear titles
  - Optional: link to the source file (e.g., Figma share URL) on the first slide
  - Notes per slide describing interactions, states, and rationale

### Where to store
- Upload the PowerPoint to the ADC Development team’s folder on Microsoft Teams
- Include a short message in the channel with:
  - Overlay name and version
  - Link to the draft overlay documentation in this repo
  - Any open questions needing feedback

### Review checklist (before sharing)
- [ ] All primary user flows are represented (happy paths)
- [ ] Error/empty/loading states shown where relevant
- [ ] Localization and accessibility considerations addressed
- [ ] Slide notes describe interactions and non-obvious behaviors
- [ ] File named correctly and uploaded to the Teams folder

## Step 3: Implement overlays in the OCA Package Library

### Purpose
Add a generator class that builds a canonical, saidified overlay for a capture base and register it in the ADC dispatcher.

### Files to add or edit
- Create src/oca_extensions/state/overlays/<overlay_name>.ts
- Edit src/oca_extensions/extensions.ts to import and dispatch your overlay
- Optional: add fixtures under bundles/ and tests in src/*.spec.ts

### Naming and type
- File/class: <OverlayName> (e.g., FileDelimiter)
- Input key in extension JSON: <overlay_name>_overlay (snake_case)
- Output key inside extension object: <overlay_name> (snake_case)
- Type string: community/overlays/adc/<overlay_name>/${OVERLAY_VERSION}

### Class template

```
// src/oca_extensions/state/overlays/<overlay_name>.ts
import { DynOverlay } from '../../extensions.js';
import { saidify } from 'saidify';
import canonicalize from '../../../utils/canonical.js';
import { OVERLAY_VERSION } from '../../../types/types.js';

class <OverlayName> {
  constructor(private dynOverlay: DynOverlay, private capture_base_digest: string) {
    if (!dynOverlay) throw new Error('a dynamic extension overlay are required');
    if (!capture_base_digest) throw new Error('capture_base_digest is required');
  }

  // One getter per output field. Validate types; canonicalize map-like objects.
  private getConfig(): any {
    const raw = (this.dynOverlay as any).config || {};
    return JSON.parse(canonicalize(raw));
  }

  private toJSON(): object {
    return {
      d: '',
      capture_base: this.capture_base_digest,
      type: `community/overlays/adc/<overlay_name>/${OVERLAY_VERSION}`,
      config: this.getConfig(),
    };
  }

  public GenerateOverlay(): string {
    const [, sad] = saidify(this.toJSON());
    return JSON.stringify(sad);
  }
}

export default <OverlayName>;
```

### Dispatcher wiring (ADC)

- In src/oca_extensions/extensions.ts:
  - Import your class
  - Add a case under Overlay.GenerateOverlay() keyed by <overlay_name>_overlay and store under <overlay_name>
```
} else if (ov_type === '<overlay_name>_overlay') {
  const inst = new <OverlayName>(this._overlay.<overlay_name>_overlay, this._capture_base_digest);
  const ov = inst.GenerateOverlay();
  overlay['<overlay_name>'] = JSON.parse(ov);
}
```

- Update the unsupported-overlays error list to include <overlay_name>_overlay.

### Canonicalization notes
- Canonicalize only map-like objects (JSON objects); arrays are insertion-ordered unless the spec mandates sorting.
- Do not mutate primitives (strings/numbers); only validate.
- Keep d: '' before SAID calculation; never pre-fill a digest.

### TypeScript tips
- Dynamic maps can narrow to never; read via (this.dynOverlay as any).field, then validate (e.g., non-empty string).
- Throw on invalid types; omit optional fields when undefined.

### CI behavior
- PR/merge to main runs install, tests, build.
- Publishing to npm occurs only on pushing a tag like vX.Y.Z.

## Step 4: Implement the overlay in the OCA Composer

### Purpose
Wire the new overlay through every user-facing surface of the OCA Composer so that it can be configured, persisted, previewed, exported, and validated end-to-end. The Composer exposes six distinct touchpoints — one per button on the landing page — and each requires its own implementation work.

The six touchpoints are:
1. **Edit Schema** — lets the user add, configure, and remove overlays
2. **View Schema** — displays the overlay configuration in the attribute table
3. **Generate Text README** — includes the overlay in plain-text schema documentation
4. **Generate Markdown README** — includes the overlay in Markdown schema documentation
5. **Generate Data Entry Excel** — includes the overlay in the schema metadata sheet
6. **Verify Data** — validates uploaded data against the overlay's rules

Work through each touchpoint in order. The first three are typically the heaviest; the last three mostly require threading already-parsed overlay data through existing generation or validation pipelines.

---

### 4.1 Edit Schema — Overlay Configuration Page

This is the largest sub-task. It covers three distinct concerns: building the UI for configuring the overlay, serializing the user's configuration into the schema JSON, and restoring that configuration when an existing schema is re-uploaded.

#### 4.1.1 Create the overlay configuration page

Every overlay that a user can add or remove from a schema has its own dedicated configuration page inside the Edit Schema section of the Composer.

- Create a new React component under the overlays directory (e.g., `src/components/overlays/<OverlayName>/`).
- The component receives the current schema state as props and emits an updated schema state via a callback.
- Follow the UI design produced in Step 2 exactly. Use existing ADC design system components (inputs, toggles, dropdowns, tables) rather than building new ones.
- If the overlay has **attribute-level configuration** (i.e., a value per schema attribute), render a table or list where each row corresponds to one attribute and the user can fill in that attribute's configuration inline.
- If the overlay has **schema-level configuration** (i.e., a single value that applies globally), render a compact form at the top of the page.
- Include add/remove controls so the user can include or exclude the overlay from the schema entirely, consistent with how all other overlays are managed in the Composer.
- Validate inputs inline: show field-level error messages for invalid values (wrong type, out-of-range, empty required field) so the user cannot save a malformed configuration.

#### 4.1.2 Wire the overlay configuration into the generated schema JSON

When the user saves or exports from the Edit Schema page, the Composer serializes the entire schema to JSON (the OCA package format). The new overlay must appear in that JSON.

- Locate the serialization function that builds the `extensions.adc.overlays` object in the output JSON — this is the same structure consumed by the OCA Package library implemented in Step 3.
- Add a branch that reads the overlay's state from the Composer's schema state object and writes it into the correct key (e.g., `extensions.adc.overlays.<overlay_name>`).
- The shape of the serialized object must match exactly what the Step 3 generator class expects as input. Cross-reference the `DynOverlay` interface and the dispatcher case added in Step 3.
- If the overlay is not present (the user did not add it), omit its key entirely from the output — do not write `null` or an empty object.

#### 4.1.3 Load the overlay for editing when a schema is uploaded

Users frequently upload a previously saved schema to continue editing it. The Composer must be able to read the overlay's configuration back out of an uploaded OCA package JSON and populate the UI with the restored values.

- Locate the deserialization function that parses an uploaded schema file and populates the Composer's schema state.
- Add a case that reads `extensions.adc.overlays.<overlay_name>` from the parsed JSON and maps it back to the Composer's internal state shape for that overlay.
- If the key is absent (an older schema that did not include this overlay), the overlay should be treated as not added — the page should be empty and opt-in controls should be in their "off" state.
- After restoring, the Edit Schema page must render identically to how it would look if the user had configured the overlay manually from scratch. There must be no data loss or display artifacts on round-trip.

#### 4.1 Checklist
- [ ] Overlay configuration component created and matches Step 2 UI design
- [ ] Attribute-level and/or schema-level configuration fields are all present and validated
- [ ] Add/remove overlay control works and is consistent with existing overlays
- [ ] Overlay is correctly serialized into the output JSON under the right key
- [ ] Output JSON shape matches the Step 3 generator's expected input
- [ ] Absent overlay is omitted (not null) from the output JSON
- [ ] Schema upload correctly restores all overlay fields in the UI
- [ ] Round-trip (save → re-upload → re-save) produces identical JSON

---

### 4.2 View Schema — Attribute Table

The View Schema page renders a read-only summary of the schema. It displays a table where each row is a schema attribute and each column represents a configured overlay property. If the new overlay has attribute-level configuration, it must appear as one or more columns in this table.

- Locate the component that builds the attribute table on the View Schema page.
- Add a column (or multiple columns, one per attribute-level field) for the new overlay. Use a short, descriptive header label consistent with the overlay's terminology.
- Read the overlay data from the schema state (same state shape populated during upload in 4.1.3).
- If the overlay has no attribute-level configuration (it is purely schema-level), there is nothing to add to the attribute table. Instead, add the overlay's schema-level values to the schema metadata summary section at the top of the View Schema page, if one exists.
- If the overlay is not present in the schema, the column should either be hidden entirely or display an empty/dash value consistently with how other optional overlays are handled.

#### 4.2 Checklist
- [ ] Attribute-level overlay fields appear as columns in the attribute table
- [ ] Schema-level overlay fields appear in the schema metadata section (if applicable)
- [ ] Column is hidden or shows empty state when the overlay is not present
- [ ] Column header labels are concise and consistent with overlay terminology

---

### 4.3 Generate Text README

The text README is a plain-text file describing all schema configurations for human readers. Every overlay in the schema must be documented in this file so that a reader without access to the Composer can fully understand the schema.

- Locate the function responsible for building the text README string.
- Add a section or block for the new overlay, following the formatting conventions already established by other overlays in that file (heading style, indentation, label/value pairs).
- If the overlay has attribute-level configuration, iterate over all attributes and list each attribute's configuration value beneath a clearly labeled attribute heading.
- If the overlay has schema-level configuration, list the fields once under the overlay's section heading.
- If the overlay is not present in the schema, omit its section entirely from the README — do not print empty sections or placeholder text.

#### 4.3 Checklist
- [ ] Overlay section added to the text README generation function
- [ ] Attribute-level values are listed per attribute
- [ ] Schema-level values are listed once under the overlay heading
- [ ] Overlay section is omitted when the overlay is not present in the schema
- [ ] Formatting is consistent with existing overlay sections in the text README

---

### 4.4 Generate Markdown README

The Markdown README is the same conceptual document as the text README but rendered with Markdown formatting (headers, tables, bold text, code fences) for display in GitHub, documentation sites, or the Composer's preview pane.

- Locate the function responsible for building the Markdown README string.
- Mirror what was done in 4.3, but apply Markdown formatting. Prefer a Markdown table for attribute-level configuration (one row per attribute, one column per overlay field) because it is more readable than a flat list when there are many attributes.
- Use the same section heading hierarchy as other overlays in the Markdown README.
- If the overlay is not present in the schema, omit its section entirely.

#### 4.4 Checklist
- [ ] Overlay section added to the Markdown README generation function
- [ ] Attribute-level values rendered in a Markdown table
- [ ] Schema-level values rendered under the overlay section heading
- [ ] Overlay section is omitted when the overlay is not present in the schema
- [ ] Formatting is consistent with existing overlay sections in the Markdown README

---

### 4.5 Generate Data Entry Excel

The data entry Excel file helps users fill in data that conforms to the schema. The **first sheet** contains metadata about the schema — this is where overlay information lives. The subsequent sheets contain data entry templates and are out of scope for overlay implementation unless the overlay directly affects data format (e.g., a delimiter overlay changes how multi-value cells are formatted).

- Locate the function that builds the first (metadata) sheet of the Excel file.
- Add one or more rows for the new overlay's configuration, following the row-label conventions already used for other overlays in that sheet (typically: one row per overlay field, with the field name in column A and the value in column B, or an equivalent structured layout).
- If the overlay has attribute-level configuration, include each attribute's value — either as additional rows grouped under the overlay label or as an additional column in the attribute metadata table, depending on which convention the metadata sheet already uses.
- If the overlay is not present in the schema, omit its rows entirely from the metadata sheet.
- Test the generated file in Excel and LibreOffice Calc to confirm it opens without warnings and the overlay rows are correctly formatted.

#### 4.5 Checklist
- [ ] Overlay metadata rows added to the first sheet of the Excel generator
- [ ] Attribute-level values are listed per attribute following the existing convention
- [ ] Schema-level values are listed once under the overlay label
- [ ] Overlay rows are omitted when the overlay is not present in the schema
- [ ] Generated file opens cleanly in Excel and LibreOffice Calc

---

### 4.6 Verify Data

Data verification is the core function of the semantic engine. When a user uploads a data file, the Composer checks each cell against the schema's constraints. The new overlay must be integrated into this verification pipeline so that its rules are enforced.

- Locate the verification/validation pipeline in the Composer. This is typically a set of per-overlay validator functions that are invoked over each data row.
- Implement a validator function for the new overlay. The function should:
  - Accept a cell value (or row, depending on scope), the overlay configuration for the relevant attribute (if attribute-level), and the full schema context.
  - Return a structured result indicating pass/fail and, on failure, a human-readable error message that will be shown to the user in the verification report.
  - Follow the same result/error shape as existing validator functions so the results can be aggregated by the shared reporting logic without modification.
- Register the new validator in the pipeline so it is invoked for every applicable attribute during a verification run.
- If the overlay is not present in the schema, the validator must be a no-op — it should not run at all, or it should return an immediate pass result, so that schemas without the overlay are not penalized.
- Consider edge cases: null/empty cell values, cells with unexpected types, attributes that are not covered by the overlay (e.g., the overlay only applies to a subset of attributes).

#### 4.6 Checklist
- [ ] Validator function implemented following the existing result/error shape
- [ ] Validator covers all rules specified in the Step 1 overlay draft (MUST/SHOULD/MAY)
- [ ] Validator is registered in the verification pipeline
- [ ] Validator is a no-op when the overlay is not present in the schema
- [ ] Edge cases handled: null values, type mismatches, uncovered attributes
- [ ] Failing cells produce a clear, user-readable error message in the verification report

---

### Step 4 overall quality checklist

Before considering Step 4 complete, verify end-to-end:

- [ ] **Round-trip**: create a schema with the overlay → save → re-upload → the overlay configuration is fully restored in the Edit Schema UI
- [ ] **View Schema**: the overlay configuration is visible in the attribute table or metadata section
- [ ] **Text README**: the overlay section is present when the overlay is configured and absent when it is not
- [ ] **Markdown README**: same as above, with correct Markdown formatting
- [ ] **Data Entry Excel**: the metadata sheet contains the overlay information; the file opens cleanly
- [ ] **Verify Data**: data that violates the overlay's rules produces clear error messages; data that conforms passes cleanly
- [ ] **No overlay, no output**: a schema without the overlay produces no trace of it in any generated artifact
- [ ] **Consistent terminology**: the overlay's name, field labels, and error messages are consistent across all six touchpoints and match the Step 1 draft
