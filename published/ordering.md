**Title**: Ordering by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Carly Huitema, Mathew Mozaffari, Steven Mugisha Mizero 

**Date released**: 28-03-2025

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

Preserving the order of attributes and entry codes can be very important for understanding data and for data entry and display. This overlay lets creators specify the ordering of attributes and entry codes of a specific schema bundle.

**Canonicalization Rules**:

The ordering overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/ordering/1.1)

Next, the overlay contains attributes_ordering and entry_code_ordering which are ordered lexicographically according to [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3). 

Inside attribute_ordering is an array of attributes which are insertion ordered. Inside entry_code_ordering are attributes which are ordered lexicographically, with arrays of entry codes for each attribute which are insertion ordered.

**Example**: 

```
"ordering":{
  "d": "EN8QvvPSwCtwbRC5VhIfgVwxeiFXVyr6NeMrx55GQEQW",
  "type": "community/overlays/adc/ordering/1.1",
  "attribute_ordering": [
    "v2",
    "a1"
  ],
  "entry_code_ordering": {
    "v2": [
      "NA",
      "001",
      "002",
      "003",
      "004",
      "005"
    ]
  }
}
```


**Rules summary**: 
 - Every attribute present in this overlay MUST be present in the capture_base of the source schema.
 - Each entry code present in this overlay MUST be present in the entry overlay of the source schema.
 - Each attribute MUST appear at most once, and MAY appear zero times.
 - Each entry code (in an entry code set) MUST appear at most once, and MAY appear zero times.


**Test case**: 

```
{"d":"EErI6o1tisBYiNdtcdamakiKiKD_C8_oFadvlfivU-2O","type":"oca_package/1.0","oca_bundle":{"v":"OCAA11JSON00042e_","bundle":{"v":"OCAS11JSON000411_","d":"ECtL6yA8FwvWETaVkjq8v0mCKjs6GncqCq2aWW4_Rfqy","capture_base":{"d":"EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI","type":"spec/capture_base/1.1","attributes":{"a1":"Text","v2":"Text"},"classification":"RDF107","flagged_attributes":[]},"overlays":{"entry":[{"d":"EKuwlJFpoc8NbKpmmYiCJsjZSE0zN6o2drjnlY_78_lO","capture_base":"EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI","type":"spec/overlays/entry/1.1","language":"eng","attribute_entries":{"v2":{"001":"Red","002":"Green","003":"Blue","004":"Yellow","005":"Orange","NA":"No answer"}}}],"entry_code":{"d":"EDZCxCzACYrbz5D_i_OjzRxMOcHU3MyypUnRwClevWws","capture_base":"EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI","type":"spec/overlays/entry_code/1.1","attribute_entry_codes":{"v2":["001","002","003","004","005","NA"]}},"meta":[{"d":"EDVqwR0-BBa0tiamgiiM8ygzsX_6BuzSFSdewmc8Bd3X","capture_base":"EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI","type":"spec/overlays/meta/1.1","language":"eng","description":"Example schema with two variables","name":"Example"}]}},"dependencies":[]},"extensions":{"adc":{"EIQhXN6TmYZDHixCkPBDu9LfM9k2u9Ek_iJmpRBszqbI":{"d":"EGohlYOKe1k8yEzRrqtGkMUcs3Hx683HRgDM1c4l87qH","type":"community/adc/extension/1.0","overlays":{"ordering":{"d":"EN8QvvPSwCtwbRC5VhIfgVwxeiFXVyr6NeMrx55GQEQW","type":"community/overlays/adc/ordering/1.1","attribute_ordering":["v2","a1"],"entry_code_ordering":{"v2":["NA","001","002","003","004","005"]}}}}}}}
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/) 
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
