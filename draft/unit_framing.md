**Title**: Unit Framing by ADC - v1.1

**Community Grouping**: community/adc/extension/v1.1

**Authors**: Carly Huitema, Paul Knowles, Steven Mugisha Mizero

**Date released**:

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**: Framing objects to ontologies and controlled vocabularies is critical for data interoperability and harmonization. This extension adds semantic context by linking schema objects to standardized external concepts. For example, a schema may use uM for units, while UCUM standardizes it as umol/L, enabling consistent interpretation across communities.

In OCA, three types of information are contextually framed:

1. **Attributes** where the schema attribute is framed to a term drawn from another concept such as an ontology or controlled vocabulary.
2. **Units** where a schema unit is framed to a unit drawn from another concept.
3. **Entry codes** where a schema entry code term is framed to a term drawn from another concept.

This overlay covers **unit framing**.

**Canonicalization Rules**:

The framing overlays begin with the canonical ordering of [extension overlays](https://github.com/agrifooddatacanada/OCA_package_standard/tree/fix/key_values_requirements?tab=readme-ov-file#oca-package-syntax-requirements).

1. `d` (digest of the overlay)
2. `capture_base` (capture base SAID the overlay is specific to)
3. `type` (`community/overlays/adc/unit_framing/1.1`)
4. `framing_metadata`, then `units`, each lexicographically ordered.

**Example**:

**Capture base:** showing the schema attributes to be framed:

```
{
  "type": "spec/capture_base/1.0",
  "digest": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
  "classification": "RDF106",
  "attributes": {
    "Albumin_concentration": "Numeric",
    "Glucose_concentration": "Numeric",
    "Sample _name": "Text",
    "Sample_type": "Text"
  },
  "flagged_attributes": []
}
```

**Unit overlay:** showing the schema units to be framed:

```
{
  "capture_base": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
  "digest": "Ec3I2X4FNoPhi7Raqo1PE3nP977BN4It-ZMmqLvc6z_w",
  "type": "spec/overlays/unit/1.0",
  "metric_system": "",
  "attribute_units": {
    "Albumin_concentration": "mg/dL",
    "Glucose_concentration": "mg/dL"
  }
}
```

#### Framing Overlays and Relationships

To describe the framing relationship between OCA schema objects and external concepts, the [Simple Standard for Sharing Ontological Mappings](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9216545/) (SSSOM) is used.

Only the four required SSSOM metadata elements are utilized:

| OCA                       | SSSOM                  |
| ------------------------- | ---------------------- |
| attribute/unit/entry_code | subject_id             |
| term_id                   | object_id              |
| predicate_id              | predicate_id           |
| framing_justification     | matching_justification |

As mentioned above, there are three types of information in OCA being framed, thus three types of framing overlays: Attribute framing overlay, Unit framing overlay, and Entry code framing overlay.

Each framing overlay type can have more than one overlay of the same variant. Each variant is of a single primary external vocabulary being used to frame the OCA schema objects.

After `d`, `capture_base`, and `type`, `framing_metadata` MUST follow.

```
// the structure of framing metadata

"framing_metadata": {
  "prefix": "dcat", // Short prefix for the primary vocabulary (reusable in term references)
  "label": "Data Catalog Vocabulary", // Label of the primary vocabulary
  "location": "http://www.w3.org/ns/dcat#", // Base URI / location of the primary vocabulary
  "version": "3", // Version of the primary vocabulary
  "imports": { // Supporting vocabularies imported by the primary vocabulary
    "dcterms": {
      "label": "DCMI Metadata Terms",
      "location": "http://purl.org/dc/terms/",
      "version": "1.1"
    },
    "foaf": {
      "label": "Friend of a Friend",
      "location": "http://xmlns.com/foaf/0.1/",
      "version": "0.1"
    }
  }
},
```

`framing_metadata` declares a **primary vocabulary** (`prefix`, `label`, `location`, `version`) and optional supporting vocabularies under `imports`. This establishes a clear hierarchy—for example, DCAT is what is being framed, while `dcterms` and `foaf` are imported to support it—while providing reusable prefixes for term references. Each key in `imports` is the supporting vocabulary's prefix; each value MUST include `label`, `location`, and `version`.

**Unit framing overlay example**

```

// canonicalization rules: d, capture_base, type, framing_metadata, units

"unit_framing": {
  "d": "XXXX",
  "capture_base": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
  "type": "community/overlays/adc/unit_framing/1.1",
  "framing_metadata": {
    "prefix": "ucum",
    "label": "Unified Code for Units of Measure",
    "location": "https://ucum.org/",
    "version": "2.1",
    "imports": {}
  },
  "units": {
    "mg/dL": {
      "framing_justification": "semapv:ManualMappingCuration",
      "predicate_id": "skos:exactMatch",
      "term_id": "mg/dL"
    }
  }
}
```

### Rules summary for unit framing overlays

- For each framing overlay there must be a `framing_metadata.prefix` identifying the primary vocabulary.
- Within each overlay framing type (attribute, unit or entry_code) each `prefix` must be unique.
- `framing_metadata.imports` MAY declare supporting vocabularies; each import key is a prefix and MUST include `label`, `location`, and `version`.
- Not every term must be framed
- For unit framing, each unit used in a schema can be framed only once.
- For unit framing, each unit can only be framed using `skos:exactMatch`

**Predicate_id**

The `predicate_id` MUST be a `skos` term for describing the relationship between the `subject_id` and `object_id`.

| Skos term         | Description                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| skos:closeMatch   | closeMatch is used to link two concepts that are sufficiently similar that they can be used interchangeably in some information retrieval applications. In order to avoid the possibility of "compound errors" when combining mappings across more than two concept schemes, skos:closeMatch is not declared to be a transitive property. |
| skos:exactMatch   | exactMatch is used to link two concepts, indicating a high degree of confidence that the concepts can be used interchangeably across a wide range of information retrieval applications. skos:exactMatch is a transitive property, and is a sub-property of skos:closeMatch.                                                              |
| skos:broadMatch   | <A> skos:broadMatch <B> where B is broader than A. broadMatch is used to state an associative mapping link between two concepts.                                                                                                                                                                                                          |
| skos:narrowMatch  | <A> skos:narrowMatch <B> where B is narrower than A. skos:narrowMatch is owl:inverseOf the property skos:broadMatch.                                                                                                                                                                                                                      |
| skos:relatedMatch | relatedMatch is used to state an associative mapping link between two concepts.                                                                                                                                                                                                                                                           |

Source: [SKOS mapping vocabulary](https://www.w3.org/TR/skos-reference/#mapping)

**Framing_justification**

The `framing_justification` MUST be a `semapv` term for describing the justification for the relationship between the `subject_id` and `object_id`.

|Semapv term|Description|
|---|---|
|semapv:MappingReview|A process that is concerned with determining if a mapping candidate (otherwise determined) is reasonable/correct.|
|semapv:ManualMappingCuration|A matching process that is performed by a human agent and is based on human judgment and domain knowledge.|
|semapv:LogicalReasoning|A matching process based on the inferences made by a logical reasoner.|
|semapv:LexicalMatching|A matching process based on a lexical comparison between one or more syntactic features of the subject with one or more syntactic features of the object.|
|semapv:CompositeMatching|A matching process based on multiple, possibly intertwined, matching approaches.|
|semapv:UnspecifiedMatching||A matching process based on an unspecified comparison.|
|semapv:SemanticSimilarityThresholdMatching||A matching process based on a minimum threshold of a score from a comparison based on a semantic similarity algorithm.|
|semapv:LexicalSimilarityThresholdMatching|A lexical matching process based on a minimum threshold of a score from a comparison based on a lexical similarity algorithm.|
|semapv:MappingChaining|A matching process based on the traversing of multiple mappings.|

Source: SEMAPV: [A Vocabulary for Semantic Mappings](https://github.com/mapping-commons/semantic-mapping-vocabulary) and [use in SSSOM](https://mapping-commons.github.io/sssom/mapping_justification/)

**Test case**:

Unit Framing Overlay

```
{"d":"EME1KjyGWqp25U_1snb5kCN7KLIDe5UBae4czFYYXOq-","type":"oca_package/1.0","oca_bundle":{"v":"OCAA11JSON0004a4_","bundle":{"v":"OCAS11JSON000487_","d":"EFPGBEwn5Hzl9Cbx1r9Od54IwhkqJXc3vE4Jm7mjvHy2","capture_base":{"d":"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l","type":"spec/capture_base/1.1","attributes":{"age":"Text","height":"Text","languages":"Text"},"classification":"","flagged_attributes":[]},"overlays":{"entry":[{"d":"EPF1_UJBIUow7nhkWxZKBazHvPRPtSw23C2A551KP0Iw","capture_base":"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l","type":"spec/overlays/entry/1.1","language":"eng","attribute_entries":{"languages":{"eng":"English","fra":"Français"}}}],"entry_code":{"d":"EH3-TrXb_zsql5SLdTIGBjZcwBkiEZ8CXEpQT4FPS-XD","capture_base":"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l","type":"spec/overlays/entry_code/1.1","attribute_entry_codes":{"languages":["eng","fra"]}},"meta":[{"d":"EP-ER88GMUXIrkk9hhF8z0IS3ji1l2mYjAXwCI-0Y06S","capture_base":"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l","type":"spec/overlays/meta/1.1","language":"eng","description":"Athlete","name":"Cricket"}],"unit":{"d":"EGGyEF22FXecJq9ShCxJcn0cBLHrNLyXEW4GrZm6ivcy","capture_base":"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l","type":"spec/overlays/unit/1.1","attribute_unit":{"height":"kg"}}}},"dependencies":[]},"extensions":{"adc":{"EJXNTP69W5wu-5ypWqLZX_nY4lQjCE2mdjw0diko-56l":{"d":"EMB8WpIeu78Gdbq9pgJ9gOJmwW5pq13TLvLOQPuqxHvR","type":"community/adc/extension/1.0","overlays":{"example":[{"d":"EIue3pCTsb60lSFkc6H71Y9zFe8R40pCuSoHJWVqP3gs","type":"community/overlays/adc/example/1.1","language":"ar","attribute_examples":{"Albumin_concentration":["52.69"],"Glucose_concentration":["8.7"],"Sample_name":["عينة"],"Sample_type":["BLD003"]}},{"d":"EKVqonNv_5Z0r0yu8tan_6lI2Bohu1cP2nSF4eE6uFPz","type":"community/overlays/adc/example/1.1","language":"eng","attribute_examples":{"Albumin_concentration":["52.69"],"Glucose_concentration":["8.7"],"Sample _name":["Carlys_sample"],"Sample_type":["BLD003"]}}],"ordering":{"d":"EDvp_MElDjSTw1nLpnKDQGsf0T3RO8L3Iaox5x0raQBZ","type":"community/overlays/adc/ordering/1.1","attribute_ordering":["age","height","languages"],"entry_code_ordering":{"languages":{"languages":["eng","fra"]}}},"range":{"d":"EJaUhjByzOY7joq5iMIJ4BcG62Ju73IMzmw70wNLBR_u","type":"community/overlays/adc/range/1.1","attributes":{"age":{"lower":"0","lower_inclusive":true,"upper":"100","upper_inclusive":true},"height":{"lower":"0","lower_inclusive":true,"upper":"300","upper_inclusive":false}}},"sensitive":{"d":"EO8ftlqoiGmbsATLr9TAxUaE8a0bRrWpM2shXwszqLG6","type":"community/overlays/adc/sensitive/1.1","sensitive_attributes":["age"]},"unit_framing":{"d":"EObBElHhoeff-oJx_bCQfav4qOZ3RCwFH7xdqzQx1qB7","type":"community/overlays/adc/unit_framing/1.1","framing_metadata":{"prefix":"ucum","label":"Unified Code for Units of Measure","location":"https://ucum.org/","version":"2.1","imports":{}},"units":{"kg":{"framing_justification":"semapv:ManualMappingCuration","predicate_id":"skos:exactMatch","term_id":"kg"}}}}}}}}
```

## Normative references

- [OCA specification v1.0.1](http://oca.colossi.network/specification/)
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
