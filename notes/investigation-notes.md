
# Investigation Notes — Detection Rule Peer Review

## Investigation Overview

Lab 33 focused on conducting an independent technical review of three YARA rules created during previous labs.

The objective was to evaluate rule quality, detection scope, logic, specificity, limitations and maintainability without modifying the original rules.

## Environment

- OS: Ubuntu 24.04 on WSL
- YARA: 4.5.0
- Workspace: `~/malware-yara-labs/lab33`
- Review date: 26 November 2026

## Rules Selected

| Rule | Source | Detection Type |
|---|---|---|
| Lab03_Broad_PowerShell_Keyword | Lab 03 | Broad string detection |
| Synthetic_Downloader_Pattern | Lab 21 | Multi-indicator string detection |
| Lab24_Exact_Custom_Byte_Pattern | Lab 24 | Exact byte-pattern detection |

The rules were copied into `reviewed-rules/` to preserve the original lab versions.

## Rule 1 — Lab03_Broad_PowerShell_Keyword

### Observed Logic

The rule uses the case-insensitive string:

`powershell`

The condition requires the keyword to be present.

### Strengths

- Simple and readable.
- Easy to maintain.
- Broad keyword identification.

### Limitations

- Low specificity.
- Legitimate scripts and documentation may match.
- The keyword alone does not establish malicious behavior.
- Files using different terminology may not match.

### Review Recommendation

Treat the rule as broad keyword discovery. Additional contextual indicators and controlled testing would be needed for a more specific detection objective.

### Preliminary Verdict

Approved with recommendations.

## Rule 2 — Synthetic_Downloader_Pattern

### Observed Logic

The rule requires the following indicators:

- `https://example.invalid/`
- `DownloadFile`
- `WriteFile`
- `CreateProcess`

The indicators are defined as case-insensitive strings.

### Strengths

- Uses a combination of indicators.
- More specific than a single keyword.
- Clearly identifies its synthetic testing purpose.
- Metadata explicitly states that it is not a malware verdict.

### Limitations

- Coverage is limited to the defined indicators.
- Different downloader implementations may not match.
- Similar strings may appear in benign scripts or documentation.
- String presence does not prove that downloading, writing or execution occurred.

### Review Recommendation

Validate the complete indicator combination, partial combinations and benign samples. Document the detection as a synthetic downloader pattern rather than generic downloader detection.

### Preliminary Verdict

Approved with recommendations.

## Rule 3 — Lab24_Exact_Custom_Byte_Pattern

### Observed Logic

The rule uses the exact byte sequence:

`C0 DE 24 A1 7F 55 0D F0`

The condition requires this sequence to be present.

### Strengths

- Clearly defined byte pattern.
- Simple condition.
- Suitable for controlled testing.

### Limitations

- Exact matching may miss modified byte sequences.
- Unrelated files may contain the same bytes.
- The match does not identify malicious behavior or intent.
- Detection coverage is limited to the defined sequence.

### Review Recommendation

Test an exact positive fixture and a modified-byte fixture. Compare the exact rule with the wildcard pattern from Lab 24 and document limitations.

### Preliminary Verdict

Approved with recommendations.

## Consolidation and Validation

The three selected rule copies were consolidated into:

`evidence/peer-review-rules.yar`

The rule count was verified as three.

YARA syntax validation was performed against `/dev/null`.

### Confirmed Results

- Rule count: 3
- YARA version: 4.5.0
- Syntax validation: Passed
- Exit status: 0

No compilation error was observed during validation.

## Review Scorecard

| Rule | Objective | Logic | Specificity | FP Risk | FN Risk | Test Evidence | Maintainability | Total |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Broad PowerShell | 2 | 2 | 0 | 1 | 1 | 0 | 2 | 8 |
| Synthetic Downloader | 2 | 2 | 1 | 1 | 1 | 0 | 2 | 9 |
| Exact Byte Pattern | 2 | 2 | 2 | 1 | 1 | 0 | 2 | 10 |

The scores are preliminary assessments based on the review criteria. Test evidence received zero because controlled testing was not completed.

## Evidence Preservation

SHA256 hashes were generated for the three reviewed rule copies and saved in:

`evidence/reviewed-rule-hashes.txt`

The hashes provide a means to identify the reviewed versions and detect subsequent changes.

## Final Assessment

All three rules passed syntax validation.

The peer review identified limitations involving broad indicators, restricted detection coverage, possible false positives, possible false negatives and lack of controlled testing.

The preliminary verdict for all three rules was Approved with recommendations.

Controlled fixture validation remains pending. The review does not establish production readiness or real-world malware detection effectiveness.
