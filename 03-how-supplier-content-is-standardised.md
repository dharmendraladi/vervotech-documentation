# How Supplier Content Is Standardised

Every room you send passes through the same standardisation pipeline before any grouping happens. Knowing what that pipeline does, and what it keys off, explains most of the results you will see.

## The pipeline

1. **Structured attributes are merged into the room text**, so the attributes you send as fields and the details a supplier wrote into the room name are considered together.
2. **The text is cleaned.** Embedded HTML is stripped, underscores and separators are normalised, and duplicated whitespace is collapsed.
3. **Non-distinguishing phrases are removed.** Marketing copy, promotional wording and rate conditions do not describe the room, so they are dropped before comparison.
4. **Tokens are normalised.** Abbreviations are expanded, common misspellings are repaired, and recognised non-English terms are mapped to their English equivalents.
5. **Attributes are extracted** into structured values: room category, bed types and counts, view, occupancy, board basis, accessibility and other room features.
6. **Rooms are grouped** on the resulting attributes.

## Standardisation is supplier aware

The `provider` value you send is not just a label. It selects how your room content is interpreted, because suppliers write room names in materially different styles, with their own abbreviations, their own noise wording and their own use of the description field.

The value is matched exactly, after trimming and lower casing.

* **A `provider` value that does not match the master list is still accepted, and no warning is returned.** It is simply interpreted without any supplier specific knowledge.
* Always source `provider` from the master provider names endpoint, and keep the value stable across requests. Changing the spelling of a provider name, or introducing a variant such as an internal code, silently changes how that supplier's content is read.
* Send one supplier's rooms under one provider name. Do not merge several suppliers behind a single value, and do not split one supplier across several.

## Board basis

Board basis is standardised from a keyword vocabulary that covers both common wording and supplier specific wording. The supported standard values, and the keywords behind them, are listed under References.

## When the vocabulary does not cover your content

The vocabulary is extended continuously from real supplier data. If you see room names that are consistently misread, or a supplier whose wording is not being interpreted well, send samples to customersuccess@vervotech.com. Real examples are far more actionable than a description of the problem.
