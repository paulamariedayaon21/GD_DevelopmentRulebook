# GD Development Rulebook - Copilot Instructions

## Purpose

This repository contains D365FO development standards used for AI-assisted code review.

When reviewing D365FO or X++ development, use the rules defined in:

`DevelopmentGuidelines/01-AICheckableRules.md`

as the authoritative development rulebook for this repository.

## Review Requirements

When asked to review D365FO or X++ code:

1. Review the supplied code against the applicable rules in `DevelopmentGuidelines/01-AICheckableRules.md`.
2. Reference the exact Rule ID for every finding.
3. Do not invent violations or missing information.
4. Only evaluate rules relevant to the supplied artifacts.
5. Use `EVIDENCE REQUIRED` when information necessary to evaluate a rule was not supplied.
6. Do not treat unavailable evidence as a violation.
7. Explain why each detected violation does not comply with the rulebook.
8. Recommend a specific correction.
9. Provide corrected X++ code when appropriate.
10. Preserve the intended business behavior of the submitted code.

## Review Status

Use only these statuses:

- PASS
- VIOLATION
- EVIDENCE REQUIRED

## Finding Format

For every violation, provide:

- Rule ID
- Severity
- Status
- File or artifact
- Line number when available
- Violation
- Explanation
- Recommendation
- Corrected example when applicable

Example:

Rule ID: AI-COMMENT-003

Severity: Medium

Status: VIOLATION

File: SampleClass.xpp

Line: 25

Violation:
The newly created method does not contain the required project reference or method description.

Explanation:
New methods must identify the development project and explain the purpose of the method.

Recommendation:
Add the project name and a meaningful description immediately above the method.

Corrected example:

// Project: VC_250911_MCB_19565_MonthlySalesDimension
// Description: Adds the lens design value to the monthly sales output.
public void addLensDesign()
{
}

## Review Summary

At the end of the review, summarize:

- Overall Result
- Rules Evaluated
- Passed
- Violations
- Evidence Required
- Critical Violations
- High Severity Violations
- Medium Severity Violations
- Low Severity Violations

Overall Result must be:

- PASS when no violations are detected.
- FAIL when one or more violations are detected.
- INCOMPLETE when no violations are detected but required evidence prevents meaningful completion of the review.

## Important Review Behavior

Do not attempt to maximize the number of findings.

The objective is to perform an accurate, evidence-based review against the GD Development Rulebook.

Do not report a rule as violated unless the supplied evidence supports the finding.

When information required for a rule is unavailable, report EVIDENCE REQUIRED instead of VIOLATION.