# Contributing to reproducr-db

`reproducr-db` is the community-maintained database of known silent breaking
changes in R packages. Every entry directly improves the usefulness of
`reproducr::risk_score()` for everyone.

**You don't need to know the `reproducr` package internals to contribute.
If you have hit a silent breaking change in a CRAN package, we want your entry.**

---

## What counts as a silent breaking change?

A silent breaking change is a package update that:

- Changed the **output values** of a function without producing an error or warning
- Changed the **default behaviour** of a function in a way that affects results
- Changed the **column names or structure** of a return value

It does **not** include:

- Changes that produce an error (those are loud, not silent)
- Deprecated arguments that still work with a warning
- New features that don't affect existing code

### Examples of valid entries

| Package | Version | Change |
|---|---|---|
| `dplyr` | 1.1.0 | `summarise()` default grouping behaviour changed |
| `readr` | 2.0.0 | `read_csv()` switched to vroom backend, column type guessing changed |
| `stringr` | 1.5.0 | `str_c()` NA handling changed to match `paste()` |
| `MatchIt` | 4.0.0 | Complete rewrite, output object structure changed |

---

## Entry format

Each entry is a JSON file in `entries/{pkg}/{pkg}__{fn}__{from_version}.json`.

### File naming

```
entries/dplyr/dplyr__summarise__1-0-99.json
         ^^^   ^^^^^  ^^^^^^^^^  ^^^^^^
         pkg   pkg    fn         from_version (dots replaced with hyphens)
```

### JSON schema

```json
{
  "pkg":          "dplyr",
  "fn":           "summarise",
  "from_version": "1.0.99",
  "to_version":   "1.2.9",
  "risk":         "high",
  "description":  "In dplyr 1.1.0, summarise() changed its default grouping behaviour...",
  "reference":    "https://dplyr.tidyverse.org/news/index.html#dplyr-110",
  "added_by":     "your-github-username",
  "added_date":   "2026-06-05"
}
```

### Field definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `pkg` | string | yes | CRAN package name, case-sensitive |
| `fn` | string | yes | Function name without parentheses |
| `from_version` | string | yes | Last safe version (exclusive lower bound) |
| `to_version` | string | yes | Last risky version (inclusive upper bound) |
| `risk` | string | yes | `"high"`, `"medium"`, or `"low"` |
| `description` | string | yes | Plain-English explanation (see guidance below) |
| `reference` | string | yes | URL to official changelog, NEWS.md, or CRAN page |
| `added_by` | string | no | Your GitHub username |
| `added_date` | string | no | Date in `YYYY-MM-DD` format |
| `closed` | boolean | no | `true` if the window is intentionally closed |
| `closed_reason` | string | no | Explanation when `closed = true` |

---

## Risk levels

| Level | When to use |
|---|---|
| `"high"` | Output **values** can change silently -- different numbers, different rows |
| `"medium"` | Argument renamed/removed, or structural change that may error or silently change output |
| `"low"` | Minor behavioural note -- output unlikely to differ in most cases |

When in doubt, prefer `"medium"` over `"high"`. A missed flag is better than
a false positive that erodes trust in the tool.

---

## Version window design principles

The version window `(from_version, to_version]` defines when a call is flagged:

```
flagged if: installed > from_version  AND  installed <= to_version
```

### Rules

**1. Permanent package changes** (e.g. dplyr 1.1.0 summarise grouping)

Set `to_version` to the current release series ceiling:

```json
"from_version": "1.0.99",
"to_version":   "1.2.9"
```

Update `to_version` when new versions release and the change still applies.

**2. Historical base R changes** (e.g. R 3.6.0 RNG change)

Close the window at the patch series where the change occurred and mark
`closed = true`:

```json
"from_version": "3.5.99",
"to_version":   "3.6.9",
"closed":       true,
"closed_reason": "All active R users are on R >= 4.x and past this change."
```

**3. Fixed in a later version**

Set `to_version` to the last version where the change applies:

```json
"from_version": "2.0.99",
"to_version":   "2.3.1"
```

### Choosing `from_version`

Set `from_version` to the last version **before** the breaking change:

- Change introduced in `dplyr 1.1.0` → `from_version = "1.0.99"`
- Change introduced in `readr 2.0.0` → `from_version = "1.4.99"`

---

## Writing a good description

The description appears in the `reproducr` risk report. It should:

1. State **which version** introduced the change
2. Describe the **old behaviour** and the **new behaviour**
3. Explain the **analytical consequence** -- what goes wrong silently

**Good example:**

> In dplyr 1.1.0, summarise() changed its default grouping behaviour: it now
> drops the last grouping level and returns an ungrouped data frame by default
> (.groups = 'drop_last'). Code that relied on the result being grouped will
> produce silently different results when chaining further group operations.

**Poor example:**

> summarise() changed in dplyr 1.1.0.

---

## Submitting an entry

1. **Fork** the repository
2. Create a new branch: `git checkout -b add-{pkg}-{fn}`
3. Create the JSON file in `entries/{pkg}/`
4. Validate locally:
   ```bash
   # The CI will validate automatically, but you can check the JSON is valid:
   python3 -m json.tool entries/dplyr/dplyr__summarise__1-0-99.json
   ```
5. Open a **Pull Request** with:
   - Title: `feat: add {pkg}::{fn} entry`
   - Description: brief explanation of the breaking change and a link to evidence

### What happens after you submit

1. CI validates the JSON schema and filename convention automatically
2. A maintainer reviews the entry for accuracy and completeness
3. On merge, the sync pipeline automatically compiles the entry into the
   `reproducr` package within minutes

---

## Updating a stale entry

If `to_version` is below the current CRAN release and the change still applies,
update `to_version` to cover the current series:

```json
"to_version": "1.2.9"   // was 1.1.9, dplyr is now at 1.2.x
```

The weekly staleness workflow opens an issue listing all stale entries.
Pick one from the issue and submit a PR.

---

## Questions

Open a discussion or issue at:
**https://github.com/repro-stats/reproducr-db/issues**