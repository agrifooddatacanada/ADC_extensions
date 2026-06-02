**Title**: Range by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Carly Huitema, Paul Knowles, Ryan Barrett

**Date released**: 25-05-2026

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

This overlay adds minimum and maximum values (inclusive or exclusive) for attributes with either numeric or date/time datatypes.

**Canonicalization Rules**:

The range overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/range/1.1)

Then there objects with the name of each attribute which are lexicographically ordered and the contents of each attribute named object are also lexicographically ordered.

**Example**: 

```
"range": {
            "d": "EMta65hl_7m-_1Arw_xDsLm5Ac1c-gFiTLxnXE8Ck46D",
            "capture_base": "EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI",
            "type": "community/overlays/adc/range/1.1",
            "attributes": {
              "attr_1": {
                "lower": "10",
                "lower_inclusive": true,
                "upper": "20",
                "upper_inclusive": true
              },
              "attr_2": {
                "lower": "2000-05-11",
                "lower_inclusive": true,
                "upper": "2005-05-11",
                "upper_inclusive": false
              }
            }
          }
```


**Rules summary**: 
- Only attributes with datatype Numeric or DateTime MAY have a range value
- A Numeric or DateTime datatype attribute MAY have a lower bound, an upper bound, or both
- If a lower and/or upper bound is given it MUST also include the appropriate lower_inclusive or upper_inclusive value which MUST be either true or false.
- The lower value MUST be lower than the upper value
- The format of the lower and upper values MUST match the format overlay


**Test case**: 

```
Intentionally left empty
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/) 
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
