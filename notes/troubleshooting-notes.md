
# Troubleshooting Notes — Detection Rule Peer Review

## Lab Environment

- Ubuntu 24.04 on WSL
- YARA 4.5.0
- Workspace: `~/malware-yara-labs/lab33`

## 1. Rule File Selection and Copying

### Objective

Copy the selected YARA rules from previous labs into the Lab 33 review workspace without modifying their original files.

### Commands Used

```bash
cd ~/malware-yara-labs/lab33

cp ../lab03/rules/broad_keyword.yar reviewed-rules/
cp ../lab21/rules/synthetic_downloader.yar reviewed-rules/
cp ../lab24/rules/custom_byte_pattern.yar reviewed-rules/
```

### Verification

```bash
ls -lh reviewed-rules/
```

### Observed Result

All three rule copies were present:

- `broad_keyword.yar`
- `custom_byte_pattern.yar`
- `synthetic_downloader.yar`

No copy error was observed.

---

## 2. Rule Consolidation

### Objective

Combine the selected rules into a single file for syntax validation.

### Command

```bash
cat reviewed-rules/*.yar > evidence/peer-review-rules.yar
```

### Verification

```bash
grep -cE '^[[:space:]]*rule[[:space:]]+' evidence/peer-review-rules.yar
```

### Observed Result

```text
3
```

The consolidated file contained three rule declarations.

---

## 3. YARA Syntax Validation

### Objective

Validate the syntax of the consolidated rule file.

### Command

```bash
yara evidence/peer-review-rules.yar /dev/null
```

### Observed Result

The command returned without a compilation error or detection output.

The validation script recorded:

- YARA version: 4.5.0
- Rule count: 3
- Exit status: 0

### Interpretation

The consolidated rules passed syntax validation.

This does not confirm that the rules detect their intended samples or avoid false positives.

---

## 4. Validation Evidence Recording

### Objective

Record rule count, YARA version and syntax-validation result.

### Command Used

```bash
{
    echo "Detection Rule Peer Review - Syntax Validation"
    echo "Date: $(date -Is)"
    echo
    echo "Rule count:"
    grep -cE '^[[:space:]]*rule[[:space:]]+' evidence/peer-review-rules.yar
    echo
    echo "YARA version:"
    yara --version
    echo
    echo "Validation:"
    yara evidence/peer-review-rules.yar /dev/null
    echo "Exit status: $?"
} | tee evidence/syntax-validation.txt
```

### Observed Result

The output was saved successfully to:

`evidence/syntax-validation.txt`

No syntax-validation failure was observed.

---

## 5. SHA256 Hash Generation

### Objective

Record hashes for the reviewed rule copies to preserve version identification.

### Command

```bash
sha256sum reviewed-rules/*.yar \
    | tee evidence/reviewed-rule-hashes.txt
```

### Observed Result

SHA256 hashes were generated for all three reviewed rule files.

The resulting evidence file:

`evidence/reviewed-rule-hashes.txt`

can be used to compare the reviewed copies with future versions.

---

## 6. Review Documentation

### Objective

Create review documentation and a scorecard for the selected rules.

### Files Created

- `reports/peer-review-checklist.md`
- `reports/peer-review-findings.md`
- `reports/review-scorecard.csv`

The checklist provides a structured assessment framework.

The findings report records rule strengths, limitations, recommendations and preliminary verdicts.

The scorecard provides a consistent scoring method across the three reviewed rules.

---

## 7. Limitations and Pending Validation

No rule compilation errors were observed during this lab.

The following items remain pending rather than being classified as resolved issues:

- Controlled positive fixture testing.
- Controlled negative fixture testing.
- Testing partial indicator combinations.
- Testing modified byte patterns.
- Empirical false-positive assessment.
- Empirical false-negative assessment.

These require additional test cases and evidence.

## Troubleshooting Conclusion

The Lab 33 rule-copying, consolidation, syntax-validation and evidence-preservation activities completed successfully.

No syntax-validation errors required troubleshooting.

The main outstanding work is controlled testing to validate detection behavior rather than syntax alone.
