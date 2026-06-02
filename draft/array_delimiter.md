**Title**: Array Delimiter by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Ali Asjad

**Date released**: 25-05-2026

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

This overlay defines how multiple values are represented within a single data field (cell) in flat/tabular files (e.g., CSV/TSV). It specifies the character used to separate elements of in-cell arrays on a per-attribute basis.

This is distinct from the file (field) delimiter configured by the file delimiter overlay. Implementations MUST avoid ambiguity between the field delimiter and the array delimiter. This overlay improves interoperability when attributes contain lists such as tags, multiple selections, or coordinate pairs.

Keys defined by this overlay:
- `attributes` (object, REQUIRED): Maps each attribute name to its in-cell array delimiter string. The delimiter MUST be exactly one Unicode code point in JSON string form (e.g., `"|"`, `";"`, `","`, `"\t"`). Only attributes with an array datatype MAY appear here.

This overlay does not change attribute semantics; it specifies how lists are serialized into single fields for file-based exchange.

**Canonicalization Rules**:

The array_delimiter overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/array_delimiter/1.1)

All other object properties MUST follow [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3). Arrays, if present, are insertion ordered. For SAID generation, JSON MUST be serialized in a fully compact form without extraneous whitespace.

**Example**:

The following is a canonicalized overlay object. SAID values are illustrative.

```
"array_delimiter": {
  "d": "EJzIjEiXnRbbKbvD9LuTN3_9l119d4X01pitgMlvPi-U",
  "capture_base": "EBoQqIshVozU3rBf27mIFncFFQd50ryjo5eJZuRQ3Nr0",
  "type": "community/overlays/adc/array_delimiter/1.1",
  "attributes": {
    "samplers": ";"
  }
}
```

**Rules summary**:

- `attributes` MUST be present and MUST contain at least one entry.
- Each attribute name in `attributes` MUST exist in the capture_base of the source schema and MUST be an array datatype.
- Each attribute delimiter value MUST represent exactly one Unicode code point as a JSON string.
- The array delimiter SHOULD NOT be the same as the file (field) delimiter unless quoting/escaping renders parsing unambiguous.
- This overlay MUST NOT alter underlying data semantics or numeric formatting (see `decimal_separator` for decimal rendering).

**Test case**:

```
Intentionally left empty
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/)
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
