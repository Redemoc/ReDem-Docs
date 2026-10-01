# ReDem Output Reference

Version: ReDem public docs (ReDem-Docs repo, main branch)
Date: 2026-08-12

Sources: OpenAPI spec, API reference, quality-check guides, cleaning guide, best-practice guide, application changelog.

## Integration modes

| Mode | Emits | Notes |
| --- | --- | --- |
| API v3 live (`POST /v3/addRespondent`) | Full API payload below | OES v3 categories, `effort`, `DES`, `respondentAttributes`, `surveyPlatform`, OES translation fields |
| API v2 live (`POST /v2/addRespondent`) | Full API payload except v3-only fields | OES v2 categories; no `effort`, `DES`, translation |
| API v1 live (`POST /addRespondent`) | Same shape as v2 | CHS `questionId` auto-generated per respondent (unreliable cross-respondent) |
| Quick Import / file upload (CSV, Excel, Decipher/AYTM/Confirmit/Quantilope/Intellisurvey exports) | App export (CSV/Excel), not API JSON | All checks except **BAS**; `DES` only if survey was created as API v3 |
| Survey-tool integrations (Decipher scripts, KeyIngress, custom API) | API payload per integration version | Live integrations use API; cleaning settings sent per respondent |
| `getRespondent` / `getAllRespondents` | Same `respondentQuality` object as completed `addRespondent` | Private API key required |

## API response envelope

All successful evaluation endpoints return this wrapper.

| Field | Type | Range | Null / absent | Modes |
| --- | --- | --- | --- | --- |
| `success` | boolean | `true` / `false` | Never null on 200 | API all versions |
| `message` | string | — | Never null on 200 | API all versions |
| `results` | object | — | Absent on error responses | API all versions |

### `results` (addRespondent synchronous)

| Field | Type | Range | Null / absent | Modes |
| --- | --- | --- | --- | --- |
| `respondentId` | string | — | Never null | API all versions |
| `status` | string | `COMPLETED` | Never null when synchronous | API all versions |
| `respondentQuality` | object | — | Present when `status` is `COMPLETED` | API all versions |

### `results` (addRespondent asynchronous)

| Field | Type | Range | Null / absent | Modes |
| --- | --- | --- | --- | --- |
| `respondentId` | string | — | Never null | API all versions |
| `status` | string | `QUEUED` | Never null | API all versions |
| `respondentQuality` | object | — | Absent until polled as `COMPLETED` | API all versions |

### `results` (getRespondent / getAllRespondents)

| Field | Type | Range | Null / absent | Modes |
| --- | --- | --- | --- | --- |
| `respondentId` | string | — | Never null | API all versions |
| `status` | string | `COMPLETED`, `QUEUED` | Never null | API all versions |
| `respondentQuality` | object | — | Absent when `status` is `QUEUED` | API all versions |

- `getAllRespondents` returns `results` as an **array** of the rows above.
- Docs also show `PROCESSING` in bulk examples; treat as in-flight, same as `QUEUED` for consumers.
- Legacy intro examples use `JOB_COMPLETED`; current OpenAPI uses `COMPLETED`.

## `respondentQuality` (core payload)

Present when evaluation finished (`status` = `COMPLETED`).

| Field | Type | Range | Null / absent | Modes |
| --- | --- | --- | --- | --- |
| `redemScore` | number | 0–100, or `-999` | Never omitted when evaluated; `-999` if evaluation failed | API + export |
| `isExcluded` | boolean | `true` / `false` | Only when `activateCleaning` was `true`; default `false` if cleaning off | API + export |
| `reasonsForExclusion` | string[] | See reasons table | Empty array `[]` when included | API + export |
| `qualityScoreSummary` | object[] | One entry per applied check type | Omitted only if no checks ran | API + export |
| `dataPointsSummary` | object[] | One entry per evaluated data point | Omitted only if no data points | API + export |

### `reasonsForExclusion` values

| Value | Meaning | Modes |
| --- | --- | --- |
| `ReDem Score Threshold` | `redemScore` below cleaning threshold | API + export |
| `Open Ended Score Threshold` | OES below threshold (with enough OES data points) | API + export |
| `Open Ended Category` | OES category count exceeded (e.g. `AI_SUSPECT`) | API + export |
| `Time Score Threshold` | TS below threshold | API + export |
| `Grid-Question Score Threshold` | GQS below threshold (with enough grid data points) | API + export |
| `Coherence Score Threshold` | CHS below threshold | API + export |
| `Behavioral Analytics Score Threshold` | BAS below threshold (with enough BAS data points) | API + export |
| `Behavioral Analytics Category` | BAS category rule triggered | API + export |
| `Manually Excluded` | User changed inclusion in app | App + export only |

- DES threshold/category exclusions exist in v3 cleaning settings; exact reason string not listed in cleaning guide (use score + `isExcluded`).
- Only **one** reason is required for exclusion; multiple may be listed.

### `qualityScoreSummary[]`

Aggregated score per quality-check **type** that was applied.

| Field | Type | Range | Null / absent | When present |
| --- | --- | --- | --- | --- |
| `qualityCheck` | string | `OES`, `TS`, `GQS`, `CHS`, `BAS`, `DES` | Never null | Per applied check |
| `score` | number | 0–100, or `-999` | Never null when evaluated | Always |
| `reason` | string | Free text | Absent except CHS (and CHS data points) | CHS only |
| `incoherentQuestions` | string[] | Question IDs | Absent except CHS | CHS only |

### `dataPointsSummary[]`

Per data-point results.

| Field | Type | Range | Null / absent | When present |
| --- | --- | --- | --- | --- |
| `qualityCheck` | string | `OES`, `TS`, `GQS`, `CHS`, `BAS`, `DES` | Never null | Always |
| `dataPointId` | string | Caller-defined ID | Never null | Always |
| `score` | number | 0–100, or `-999` | Never null when evaluated | Always |
| `category` | string | See category tables | Absent for TS; present for OES, BAS, DES | OES, BAS, DES |
| `effort` | string | `LOW`, `MEDIUM`, `HIGH` | Absent unless OES v3 + (`VALID_ANSWER` or `NO_ANSWER`) | API v3 only |
| `reason` | string | Pattern label | Absent except GQS (and CHS data point) | GQS, CHS |
| `translatedAnswer` | string | English text | Absent unless translation requested and succeeded | API v3 OES only |
| `translationFailed` | boolean | `true` / `false` | Absent unless `shouldTranslate` was `true` | API v3 OES only |

### Sentinel: `-999`

- Returned for any score (including `redemScore`) when the respondent **cannot be evaluated**.
- `status` may still be `COMPLETED`.
- No AI explanation is generated when scores are unavailable.

## OES categories

### API v3 (OES v3)

| Category | Typical score impact | Modes |
| --- | --- | --- |
| `VALID_ANSWER` | Higher; modulated by `effort` | API v3, app v3 surveys |
| `NO_ANSWER` | Lower; modulated by `effort` | API v3 |
| `OFF_TOPIC` | Low | API v3 |
| `GIBBERISH` | Very low | API v3 |
| `AI_SUSPECT` | Low | API v3 |
| `BAD_LANGUAGE` | Low | API v3 |
| `WRONG_LANGUAGE` | Low | API v3 |
| `DUPLICATE_ANSWER` | Low | API v3 |
| `DUPLICATE_RESPONDENT` | Low | API v3 |

### API v1/v2 (OES v2, legacy)

| Category | Maps from (v3) |
| --- | --- |
| `GENERIC_ANSWER` | — |
| `NO_INFORMATION` | → `NO_ANSWER` in v3 |
| `BAD_LANGUAGE` | same |
| `NONSENSE` | → `GIBBERISH` |
| `DUPLICATE_ANSWER` | same |
| `DUPLICATE_RESPONDENT` | same |
| `WRONG_TOPIC` | → `OFF_TOPIC` |
| `WRONG_LANGUAGE` | same |
| `AI_GENERATED_ANSWER` | → `AI_SUSPECT` |

## BAS categories (data point)

| Category | Score note | Modes |
| --- | --- | --- |
| `NATURAL_TYPING` | ≥ 50; swipe/voice dictation forced to 60 | API live only |
| `UNNATURAL_TYPING` | < 50 | API live only |
| `COPY_AND_PASTE` | 0 (100% paste events) | API live only |
| `NATURAL_MOVEMENT` | ≥ 50 | API live only |
| `UNNATURAL_MOVEMENT` | < 50 | API live only |

- When typing and mouse both exist, the **lower** score wins; equal → typing category used.

## DES categories (API v3 only)

| Category | Score | Modes |
| --- | --- | --- |
| `VALID_ENTRANT` | ≥ 40 (typically higher) | API v3 |
| `DUPLICATE_ENTRANT` | < 40 | API v3 |
| `DUPLICATE_IP` | 0 | API v3 |
| `N/A` | Not computed (< 4 demographics) | API v3 |

## App export (CSV / Excel)

Export mirrors the **Respondent Table** (only visible columns export).

### Respondent-level export columns (documented)

| Column / field | Type | Range | Null | Modes |
| --- | --- | --- | --- | --- |
| Respondent ID | string | — | Never | All imports + API |
| ReDem Score | number | 0–100 or `-999` | — | All |
| OES / CHS / GQS / TS / BAS / DES aggregate scores | number | 0–100 or `-999` | Absent if check not configured | Per survey setup |
| Included / Excluded (cleaning status) | boolean / label | — | — | All with cleaning |
| Reasons for Exclusion | string (comma-separated) | See reasons table | Empty when included | All |
| Explanation | string | AI paragraph | Absent until generated; column appears only if any respondent has one | App only |
| Custom `respondentAttributes` keys | string | — | Per attribute | API v3 + import |
| Per-question OES score / category / effort | varies | Per data point | Dynamic columns per configured OES questions | All except effort = v3 only |
| Per-question GQS score / reason | varies | Per grid | Dynamic | All |
| Per-question TS score | number | 0–100 | Dynamic | All |
| Per-question BAS score / category | varies | Per BAS point | Dynamic; **no BAS on Quick Import** | API live |
| Translated answer | string | — | When translation enabled (v3) | API v3 |

### Excel-only extra sheets

| Sheet | Contents | Modes |
| --- | --- | --- |
| OES | Open-ended detail | Export |
| GQS | Grid detail | Export |
| TS | Time detail | Export |
| CHS | Coherence detail | Export |

- No separate BAS or DES sheet documented.
- Export respects table filters (included/excluded subset).

## Composite ReDem Score (`redemScore`)

| Fact | Detail |
| --- | --- |
| Definition | Weighted average of **applied** quality-check aggregate scores (0–100) |
| Checks included | Only checks actually configured and evaluated for that respondent |
| Weights | Dynamic per respondent; visible in app respondent detail (Overview) for surveys created after v1.6.1 |
| Weight not in API | Per-check weights are **not** returned in API/export payload |
| TS down-weight | When many TS data points exist but few other checks, TS carries **less** weight in composite (v1.6.4) |
| Exact formula | **Not published** in public docs |

### Default cleaning thresholds (recommended settings)

| Check | Default exclude if |
| --- | --- |
| ReDem Score | < 60 |
| OES | < 40 (≥ 2 open-ends) or ≥ 2 hits on selected OES categories |
| CHS | < 30 |
| GQS | < 20 (≥ 2 grids) |
| TS | < 30 |
| BAS | < 20 (≥ 2 BAS points) or category rules |
| DES | < 40 (v3; category rules off by default) |

- Cleaning rules are **OR** conditions; any single hit excludes.

## Score interpretation: 0 vs 50 vs 100

One fact per anchor; higher = more trust unless noted.

### ReDem Score (R-Score)

| Value | Meaning |
| --- | --- |
| 0 | No trust; treat as fraudulent |
| 50 | Doubtful (yellow band 40–59); below recommended keep threshold (60) |
| 100 | Complete trust |

### Open-Ended Score (OES)

| Value | Meaning |
| --- | --- |
| 0 | Worst aggregate open-end quality |
| 50 | Mixed quality; above default cleaning floor (40) but not safe alone |
| 100 | Strong open-end quality across evaluated answers |

- Per-answer category dominates; `effort` shifts scores for `VALID_ANSWER` / `NO_ANSWER` (v3).

### Coherence Score (CHS)

| Value | Meaning |
| --- | --- |
| 0 | Severe contradictions / implausible interview |
| 50 | Moderate inconsistency (scoring stabilized v1.6.0) |
| 100 | Highly coherent, plausible response set |

### Time Score (TS)

| Value | Meaning |
| --- | --- |
| 0 | Extreme timing outlier vs study median |
| 50 | Noticeable deviation; also historical floor for very slow completes (now min 30 for slowest) |
| 100 | Timing aligned with peer respondents |

- Requires **≥ 30 respondents** in survey before TS is calculated.

### Grid-Question Score (GQS)

| Value | Meaning |
| --- | --- |
| 0 | Strong straightlining / suspicious pattern |
| 50 | Some pattern concern |
| 100 | Natural-looking grid behavior |

- Pattern checks meaningful from **≥ 7** grid items; minimum **5** items to score.

### Behavioral Analytics Score (BAS)

| Value | Meaning |
| --- | --- |
| 0 | Full paste (`COPY_AND_PASTE`) or worst interaction |
| 50 | Boundary: `< 50` = unnatural typing/movement; `≥ 50` = natural |
| 100 | Strongly human-like interaction |

- Swipe typing and voice dictation scored **60** (`NATURAL_TYPING`), intentionally conservative.

### Duplicate Entrance Score (DES)

| Value | Meaning |
| --- | --- |
| 0 | `DUPLICATE_IP` match |
| 50 | Above duplicate-entrant floor (40) but may still be weak |
| 100 | First entrant in window or no duplicate signal |

## Where each score misleads

### ReDem Score

- Hides which check failed; a respondent can pass R-Score but fail a category rule (e.g. `AI_SUSPECT` count).
- Weights are opaque in API/export; two respondents with same subscores can differ in composite.
- TS can dominate or be down-weighted depending on data-point mix.

### Open-Ended Score

- Generic questions ("anything else?") make most answers look valid.
- Unaided brand recall without keywords/country context causes wrong `OFF_TOPIC` / `WRONG_LANGUAGE`.
- AI-Suspect skipped when combined open-end text is too short.
- Duplicate detection disabled or implausible duplicates expected → false flags.
- Short answers (< 10 chars, or < 3 for CJK) skip duplicate-answer check.

### Coherence Score

- Missing routing/skip context flags skipped questions as inconsistency.
- Numeric answer codes without labels reduce accuracy.
- Questions about post-training-cutoff events need `surveyDescription`.
- Designed to reduce false positives vs trap questions; can miss subtle fraud.

### Time Score

- Total LOI unreliable with routing, filters, or breaks included.
- Bots can wait at end; page/question timings are more reliable.
- Few TS data points → high variance.
- Not calculated until N ≥ 30.

### Grid-Question Score

- All-positive or all-negative item blocks punish genuine uniform attitudes as straightlining.
- Pattern check needs original display order and ≥ 7 rows.
- Few rows/columns → weaker detection.

### Behavioral Analytics Score

- **Not available on Quick Import** — file-only workflows have no BAS signal.
- Shared call-center IP irrelevant here; BAS is interaction-based.
- Swipe/voice capped at 60 — may under-penalize some edge cases by design.
- Requires ≥ 3 keystroke/paste events for typing score.

### Duplicate Entrance Score

- Local/small samples: coincidental demographic overlap → false `DUPLICATE_ENTRANT`.
- CATI with shared office IP → false `DUPLICATE_IP` if `ip` sent.
- Screening/quota demographics and constants (e.g. single-country `country`) weaken or distort matching.
- `< 4` demographics → `N/A`, not a quality pass.
- 15-minute peer window only; duplicates outside window missed.
