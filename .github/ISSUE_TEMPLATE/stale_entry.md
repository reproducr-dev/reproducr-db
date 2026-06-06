---
name: Stale entry -- to_version needs updating
about: Report a database entry whose to_version ceiling is below the current CRAN release
title: '[STALE] pkg::fn -- to_version x.x.x below current release y.y.y'
labels: stale-entry
assignees: ''
---

## Entry
`pkg::fn` (e.g. `dplyr::summarise`)

## Current `to_version` in database
e.g. `1.1.9`

## Current CRAN version
e.g. `1.3.0`

## Does the breaking change still apply in the new version?
- [ ] Yes -- extend `to_version` to cover the new release series
- [ ] No -- the change was fixed or reverted; lower or remove `to_version`
- [ ] Unsure -- needs investigation

## Reference for your assessment
Link to the relevant changelog entry or issue: