# Backup Validation & Subsystem Reliability Script

## Overview

This repository contains an anonymized, simplified version of a production-grade KornShell script used to manage and validate enterprise monthly backup systems. It was developed as part of a larger batch processing environment where backup continuity directly affected downstream operational processes.

The script detects incomplete backup sets, flags or removes invalid archives, and executes a fallback recovery workflow when no valid current-month data exists — all without requiring manual intervention for routine failures.

---

## Key Features

**Set-Based Validation**
Validates backup archives at the *set* level rather than the individual file level. This reduces iteration count as archive volume grows and makes the logic resilient to partial failures within a set.

**Predictable Failure Handling**
When no complete current-month backup set is found, the script falls back to the most recent valid set from the prior month, adjusting for month-end and leap year boundaries automatically.

**Dry-Run Safety**
Remediation commands (`rm`, `cp`) are printed to output rather than executed directly. This allows operator review before any destructive action is taken — a deliberate design choice for production safety.

**Automated Diagnostics**
Differentiates between execution failures (the script couldn't run) and process failures (the script ran but found no valid data), providing clear, actionable output for each case.

**Calendar Edge Case Handling**
Correctly handles 30-day months, 28/29-day February including leap year logic (with century and 400-year rules), and year-boundary rollover when checking prior-month archives.

---

## Design Decisions

Validation operates on backup *sets* rather than individual files. If each set contains five files and one hundred sets exist, the script performs one hundred iterations rather than five hundred. As archive volume increases over time, this gap widens without any modification to the core logic.

Fallback logic is structured using mutual recursion between `clean()` and `core()`, which separates validation state from recovery state cleanly. A flag-based approach would have worked but would have flattened the logic in a way that made the fallback path harder to follow and maintain.

Remediation output is intentionally dry-run. In a production backup environment, an automated script that silently deletes archives is a liability. Printing the commands instead keeps a human in the loop for destructive operations while still automating the diagnostic work.

---

## Technologies

- KornShell (ksh)
- Shell scripting for file system operations and archive validation
- Production-style failover handling and operational logging patterns

---

## Impact

- Reduced manual intervention for routine monthly backup failures
- Improved reliability and predictability of a critical batch processing subsystem
- Designed to scale gracefully as backup archive volume increases, without modification to core logic
- Demonstrated ability to analyze, automate, and stabilize production processes with safety constraints built in

---

## Notes

This version is anonymized for public release. Sensitive identifiers, directory structures, and environment-specific details have been removed. The core validation and fallback logic mirrors the approach used in production.
