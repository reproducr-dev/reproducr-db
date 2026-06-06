---
name: New breaking-change entry
about: Propose a new entry for the reproducr breaking-changes database
title: '[DB] pkg::fn -- brief description of the change'
labels: new-entry
assignees: ''
---

## Function
`pkg::fn` (e.g. `dplyr::summarise`)

## Version window
- **Last safe version** (`from_version`): e.g. `1.0.99`
- **Last risky version** (`to_version`): e.g. `1.1.9`

## Risk level
- [ ] **high** -- output values change silently with no error
- [ ] **medium** -- argument renamed/deprecated; may error or produce different output
- [ ] **low** -- minor behavioural note; output unlikely to differ in practice

## Description
Plain-English explanation of what changed and how it affects reproducibility.

- Which version introduced the change?
- What was the old behaviour?
- What is the new behaviour?
- What is the practical consequence for analytical results?

## Reference
Link to the official `NEWS.md`, CRAN page, or GitHub release:

## Verification
How did you confirm this? (tested on versions X and Y, linked issue, etc.)