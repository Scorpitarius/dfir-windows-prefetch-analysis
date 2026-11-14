# DFIR-003 | Windows Prefetch Artifact Investigation

## Investigation Overview

**Case ID:** DFIR-003  
**Investigation Dates:** November 1–4, 2026  
**Investigation Type:** Windows Execution Artifact Analysis  
**Classification:** Controlled Laboratory Exercise  
**Status:** Completed

This investigation examined a Windows Prefetch artifact associated with `NOTEPAD.EXE` to determine whether it contained evidence of program execution.

A baseline examination established that the Prefetch artifact existed before a controlled Notepad execution experiment. Following the experiment, the artifact's filesystem modification timestamp had advanced.

The artifact was preserved, copied for analysis, and examined using PECmd v1.5.0.0. The parser identified a recorded run count of 11 and a most recent execution timestamp of November 2, 2026, at 21:12:56 UTC.

The findings support historical Notepad execution activity and are consistent with the controlled experiment. However, the precise launch timestamp was not independently recovered.

## Investigation Objectives

- Verify that Windows Prefetch was enabled.
- Establish a baseline inventory of Prefetch artifacts.
- Perform a controlled Notepad execution.
- Preserve the associated Prefetch artifact.
- Verify evidence integrity using SHA-256.
- Examine the Prefetch file header and execution metadata.
- Document findings and investigative limitations.

## Laboratory Environment

| Component | Configuration |
|---|---|
| Operating System | Windows 10 |
| Virtualization | VMware Workstation Pro |
| Network | Isolated host-only laboratory network |
| Analysis Tool | PECmd v1.5.0.0 |
| Additional Tools | Windows PowerShell, Get-FileHash, Get-ChildItem, Start-Process |
| Hash Algorithm | SHA-256 |
| Artifact | NOTEPAD.EXE-C5670914.pf |

## Examination Methodology

### 1. Prefetch Baseline Examination

Examined the Windows Prefetch configuration and confirmed:

- `EnablePrefetcher = 3`
- 188 `.pf` files in `C:\Windows\Prefetch`

Exported the baseline inventory to `prefetch-baseline.csv`.

Examined the existing Notepad Prefetch artifact before the controlled execution.

### 2. Controlled Execution

Launched Notepad using PowerShell:

```powershell
Start-Process notepad.exe
```

Allowed Notepad to run briefly, then closed the application.

Examined the associated Prefetch artifact afterward and observed that its filesystem modification timestamp had advanced.

The precise timestamp of the controlled launch was not independently recovered.

### 3. Evidence Preservation

Copied the Notepad Prefetch artifact into the case's evidence directory and created a separate working copy for analysis.

Used SHA-256 hashing to compare the source and preserved evidence during collection and the preserved evidence and working copy during verification.

The final evidence-to-working-copy comparison returned `True`.

### 4. Prefetch Header Examination

Examined the first 16 bytes of the Prefetch artifact.

The header began with:

`4D-41-4D-04`

This was consistent with a compressed Windows Prefetch artifact.

The header indicated an uncompressed size of 48,152 bytes, consistent with the size reported by PECmd.

### 5. Prefetch Parsing

Analyzed the working copy using PECmd v1.5.0.0.

Reviewed execution metadata, recorded run count, historical timestamps, and artifact characteristics.

Exported the parsed results to `notepad-prefetch-analysis.csv`.

### 6. Evidence Integrity Verification

Generated a SHA-256 manifest covering the preserved Prefetch evidence, working copy, and PECmd analysis CSV.

Confirmed that the preserved evidence and working copy contained identical bytes at the time of verification.

## Investigation Findings

| Finding | Observed Result |
|---|---|
| Executable | NOTEPAD.EXE |
| Prefetch identifier | C5670914 |
| Prefetch format | Windows 10 or Windows 11 |
| Uncompressed size | 48,152 bytes |
| Recorded run count | 11 |
| Most recent recorded execution | 2026-11-02 21:12:56 UTC |
| Baseline LastWriteTimeUtc | 2026-11-01 18:28:32 UTC |
| Evidence LastWriteTimeUtc | 2026-11-02 21:13:06 UTC |
| Evidence-to-working-copy SHA-256 | Match |

The artifact contained seven additional historical execution timestamps.

The most recent recorded execution occurred approximately 10 seconds before the artifact's observed filesystem modification timestamp.

## Analysis and Interpretation

The Prefetch artifact and PECmd results support the conclusion that Windows recorded historical execution activity associated with Notepad.

The recorded run count of 11 represents the value maintained in the Prefetch artifact. It does not establish that Notepad was launched 11 times during this investigation.

The updated filesystem modification timestamp and most recent recorded execution are consistent with the controlled experiment.

However, because the precise timestamp of the `Start-Process` command was not independently recovered, the most recent recorded execution cannot conclusively be attributed to that specific launch.

The Prefetch identifier `C5670914` is not a cryptographic integrity hash and should not be confused with SHA-256 evidence verification.

## Limitations

- Windows Prefetch provides evidence of program execution but does not independently establish user intent or actions performed within an application.
- The absence of a Prefetch artifact does not prove that an executable was never run.
- The precise timestamp of the controlled Notepad launch was not independently recovered.
- The examination occurred on a live Windows virtual machine, where ordinary system activity and tool installation could affect artifacts.
- Matching SHA-256 values establish content consistency at verification time but do not independently establish that the source artifact was unchanged before collection.

## Conclusion

This investigation identified, preserved, and analyzed a Windows Prefetch artifact associated with `NOTEPAD.EXE`.

PECmd reported 11 recorded executions and a most recent execution timestamp of November 2, 2026, at 21:12:56 UTC.

The artifact's modification timestamp advanced following the baseline examination, and the findings were consistent with the controlled execution experiment.

The investigation demonstrates how Windows Prefetch artifacts can support execution-timeline reconstruction when combined with baseline observations, forensic parsing, and documented evidence-integrity checks.


## Supporting Documentation

### Investigation Report

[View DFIR-003 Windows Prefetch Artifact Investigation Report](Case-003-Prefetch-Investigation.pdf)

The five-page forensic investigation report includes:

- Prefetch configuration and baseline examination.
- Controlled Notepad execution experiment.
- Evidence preservation and SHA-256 integrity verification.
- Prefetch file header examination.
- PECmd execution history and artifact analysis.
- Four embedded investigation figures.
- Findings, limitations, and conclusions.

### Supporting Artifacts

Additional Prefetch evidence, analysis exports, integrity records, and investigation notes will be added after review.
