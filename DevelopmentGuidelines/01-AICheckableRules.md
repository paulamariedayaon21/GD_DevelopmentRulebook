# AI-Checkable D365FO Development Rules

## Purpose

These rules define the D365FO development standards that an AI reviewer can validate using project names, source code, comments, and check-in descriptions.

Each rule has a unique Rule ID so that AI review findings can identify the exact rule that was violated.

The AI reviewer must not assume that a rule has been violated when the information required to evaluate the rule is unavailable.

Possible review statuses are:

- PASS - The available evidence satisfies the rule.
- VIOLATION - The available evidence does not satisfy the rule.
- EVIDENCE REQUIRED - The reviewer does not have enough information to determine compliance.

---

# 1. Project Standards

## AI-PROJECT-001: Project Naming Convention

**Severity:** High

Every D365FO development project must use the following naming convention:

`VC_YYMMDD_DevInitials_TicketNumber_Description`

Example:

`VC_250911_MCB_19565_MonthlySalesDimension`

The project name must contain:

- `VC_` prefix
- Development date in `YYMMDD` format
- Developer initials
- Ticket number
- Meaningful project description

### AI Validation

The AI reviewer must flag a VIOLATION when:

- The project name does not start with `VC_`.
- The development date is missing or does not follow `YYMMDD`.
- Developer initials are missing.
- The ticket number is missing.
- The project description is missing.

If no project name is provided to the reviewer, return:

`EVIDENCE REQUIRED`

---

## AI-PROJECT-002: Project Name Consistency

**Severity:** Medium

The same project name must be used consistently throughout the development artifacts.

The project name should be consistent in:

- Code customization comments
- New method comments
- Check-in comments
- Technical Design Document references, when provided

### AI Validation

If multiple project names are found, the AI reviewer must compare them.

Return a VIOLATION when conflicting project names are detected.

The finding must show:

- Expected project name
- Detected project name
- Location of the mismatch

If the required artifacts are not available, return:

`EVIDENCE REQUIRED`

---

# 2. Code Comment Standards

## AI-COMMENT-001: Multiline Customization Comment

**Severity:** Medium

A multiline customization must be enclosed by customization comments.

Required structure:

```xpp
// Begin customization
// Project: VC_YYMMDD_DevInitials_TicketNumber_Description
// Change: Description of the change

// Customized code

// End customization
```

### AI Validation

The AI reviewer must flag a VIOLATION when:

- `Begin customization` is missing.
- `End customization` is missing.
- The project name is missing.
- The change description is missing.
- The customization markers do not enclose the modified code correctly.
- The project reference conflicts with the supplied project name.

The AI reviewer should identify the affected file and line numbers whenever available.

---

## AI-COMMENT-002: Single-Line Customization Comment

**Severity:** Medium

A single-line customization must contain a reference to the development project.

Example:

```xpp
salesLine.CustomField = value; // VC_250911_MCB_19565_MonthlySalesDimension
```

### AI Validation

The AI reviewer must flag a VIOLATION when a single-line customization is identified but the project reference is missing.

If a project reference exists, the reviewer must also validate it against:

`AI-PROJECT-001`

and, where another project name is available:

`AI-PROJECT-002`

---

## AI-COMMENT-003: New Method Documentation

**Severity:** Medium

A newly created method must contain:

- Project name
- Meaningful method description

Example:

```xpp
// Project: VC_250911_MCB_19565_MonthlySalesDimension
// Description: Adds the lens design value to the monthly sales output.
public void addLensDesign()
{
}
```

### AI Validation

The AI reviewer must flag a VIOLATION when:

- The project reference is missing.
- The method description is missing.
- The description does not explain the purpose of the method.
- The project reference conflicts with the supplied project name.

The AI reviewer must not flag existing methods merely because they lack this documentation unless evidence indicates that the method is newly created as part of the reviewed change.

---

## AI-COMMENT-004: Meaningful Change Description

**Severity:** Low

Customization comments must explain what was changed.

Descriptions should clearly identify the business or technical purpose of the customization.

### Weak Examples

```text
Changed code
Updated method
Fixed issue
Added logic
Modification
New development
```

These descriptions do not provide sufficient information about the purpose of the change.

### Good Example

```text
Add lens design column to the monthly sales output
```

### AI Validation

The AI reviewer must flag descriptions that are:

- Empty
- Generic
- Unrelated to the modified code
- Too vague to explain the purpose of the change

The reviewer must suggest a more meaningful description whenever possible.

---

# 3. Check-In Standards

## AI-CHECKIN-001: Check-In Comment Format

**Severity:** High

The check-in comment must follow this format:

`ProjectName - ChangeDescription`

Example:

`VC_250911_MCB_19565_MonthlySalesDimension - Add new lens design column`

### AI Validation

The AI reviewer must flag a VIOLATION when:

- The project name is missing.
- The project name does not follow `AI-PROJECT-001`.
- The separator between project name and description is missing.
- The change description is missing.
- The change description is vague.
- The project name conflicts with the development project supplied for review.

If the check-in comment was not supplied to the reviewer, return:

`EVIDENCE REQUIRED`

Do not assume that the check-in comment is incorrect simply because it was not provided.

---

## AI-CHECKIN-002: Ticket Traceability

**Severity:** High

The ticket number must be traceable through the project information.

When both values are available, the ticket number referenced by the change must match the ticket number contained in the project name.

Example project:

`VC_250911_MCB_19565_MonthlySalesDimension`

Expected ticket:

`19565`

### AI Validation

When sufficient information is provided, the AI reviewer must:

1. Extract the ticket number from the project name.
2. Extract the ticket number from other supplied metadata where applicable.
3. Compare the values.
4. Report a VIOLATION if the values conflict.

Example violation:

```text
Project ticket: 19565
Submitted ticket: 19566
Result: VIOLATION
```

If there is no independent ticket information available for comparison, return:

`EVIDENCE REQUIRED`

---

# 4. Cross-Rule Validation

## AI-CROSSCHECK-001: Development Traceability

**Severity:** High

Where sufficient information is available, the AI reviewer should establish traceability between:

`Ticket -> Project -> Code Comment -> Check-In Comment`

Example:

```text
Ticket:
19565

Project:
VC_250911_MCB_19565_MonthlySalesDimension

Code Comment:
VC_250911_MCB_19565_MonthlySalesDimension

Check-In:
VC_250911_MCB_19565_MonthlySalesDimension - Add new lens design column
```

### AI Validation

Return PASS when the available identifiers are consistent.

Return VIOLATION when conflicting identifiers are detected.

Return EVIDENCE REQUIRED when there is insufficient information to perform the comparison.

---

# 5. AI Review Behavior

The AI reviewer must follow these principles when applying this rulebook.

## AI-REVIEW-001: Evidence-Based Findings

The AI reviewer must only report a rule as violated when sufficient evidence exists.

The AI reviewer must not invent:

- Missing project names
- Ticket numbers
- Check-in comments
- Developer information
- TDD contents
- Code changes
- Deployment status

When required information is unavailable, return:

`EVIDENCE REQUIRED`

---

## AI-REVIEW-002: Do Not Report Unrelated Rules

Only report rules relevant to the supplied artifact.

Examples:

If only source code is supplied:

- Review applicable code and comment rules.
- Do not claim that the check-in comment failed.

If a project name is supplied:

- Review applicable project naming rules.

If a check-in comment is supplied:

- Review applicable check-in rules.

If multiple artifacts are supplied:

- Perform cross-rule validation where possible.

---

## AI-REVIEW-003: Explain Every Violation

Every VIOLATION must include:

1. Rule ID
2. Severity
3. Status
4. File or artifact reviewed
5. Line number, when available
6. Detected violation
7. Explanation
8. Recommended correction
9. Corrected example, where applicable

---

# 6. Required AI Review Output

Use the following format for each finding:

```text
Rule ID:
AI-COMMENT-003

Severity:
Medium

Status:
VIOLATION

File:
SampleClass.xpp

Line:
25

Violation:
The newly created method does not contain the required project reference or method description.

Explanation:
New methods must identify the development project and explain the purpose of the method.

Recommendation:
Add the project name and a meaningful description immediately above the new method.

Corrected example:

// Project: VC_250911_MCB_19565_MonthlySalesDimension
// Description: Adds the lens design value to the monthly sales output.
public void addLensDesign()
{
}
```

---

# 7. Review Summary

At the end of the review, provide a summary using this format:

```text
D365FO Development Rulebook Review

Overall Result: FAIL

Rules Evaluated: 6
Passed: 3
Violations: 2
Evidence Required: 1

High Severity Violations: 1
Medium Severity Violations: 1
Low Severity Violations: 0
```

`Overall Result` should be:

- `PASS` when no violations are detected.
- `FAIL` when one or more violations are detected.
- `INCOMPLETE` when no violations are detected but required evidence prevents meaningful completion of the review.

---

# 8. Severity Levels

## Critical

Use when the detected issue creates significant:

- Security risk
- Data integrity risk
- Production stability risk
- Deployment risk

## High

Use when a mandatory development standard is violated and the issue affects:

- Traceability
- Required development controls
- Deployment readiness
- Correct identification of the development change

## Medium

Use when the issue reduces:

- Code maintainability
- Documentation quality
- Reviewability
- Development consistency

## Low

Use for minor documentation or consistency issues that do not materially affect the functionality of the development.

---

# 9. Review Objective

The objective of the AI review is not simply to find as many issues as possible.

The objective is to provide a consistent, evidence-based review against the GD D365FO Development Rulebook so that developers can identify and correct issues before code proceeds through the check-in, merging, build, and deployment process.