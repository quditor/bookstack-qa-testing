# BookStack Demo - QA Testing Project

**Author:** Fuad Hasan
**GitHub:** [quditor](https://github.com/quditor)  
**Date:** 2026-10-30  
**Application under test:** [BookStack Demo](https://demo.bookstackapp.com/)  
**Feature tested:** Book Management  

---

## Project Overview

This repository documents the quality assurance testing of the **Book Management** workflow in BookStack, an open-source documentation and knowledge base platform. The project has three parts:

1. **Functional testing** of the Book Management workflow (positive, negative, boundary value and edge case tests)
2. **Bug reporting** for defects found during testing, with reproduction steps and evidence
3. **Automation candidates** - three scenarios selected for automation, with detailed test cases and justifications

## Application Under Test

| Field | Details |
|---|---|
| Application | BookStack Demo |
| URL | https://demo.bookstackapp.com/ |
| Type | Open-source wiki / knowledge base platform |
| Testing scope | Black-box functional testing |

## Selected Workflow and Rationale

**Workflow: Book Management (Create, Edit, Delete, Search)**

This workflow was selected because it:

- Is the core content workflow in BookStack: chapters and pages all depend on books
- Has several input fields (name, description, tags, cover image) that give a rich testing surface
- Supports all four required test types with meaningful coverage
- Has clear validation rules that can be tested with valid and invalid input
- Includes secondary interactions (search, sort, delete confirmation) useful for edge cases

## Tools and Approach

| Item | Details |
|---|---|
| Test environment | BookStack public demo, Admin account, Chromium browser |
| Testing type | Black-box functional testing, including edge-case testing |
| Documentation | Markdown (GitHub), with Excel working copies |
| Evidence | Timestamped screenshots for executed test cases and for each bug |
| Proposed automation framework | Playwright (JavaScript) |

Test cases were designed in four categories: positive, negative, boundary value and edge case. Each case records preconditions, steps, expected result, actual result, status, severity and evidence. Results were recorded from observed behavior on the live demo instance.

## Results Summary

| Metric | Result |
|---|---|
| Total test cases | 23 |
| Passed | 22 |
| Failed | 1 (TC-016) |
| Blocked / Not executed | 0 |
| Bugs reported | 2 |

**By test type**

| Type | Test Case IDs | Count |
|---|---|---|
| Positive | TC-001 to TC-006 | 6 |
| Negative | TC-007 to TC-011 | 5 |
| Boundary value | TC-012 to TC-015 | 4 |
| Edge case | TC-016 to TC-023 | 8 |

**By severity:** 7 High, 11 Medium, 5 Low.

**Bugs found**

| Bug ID | Title | Severity | Priority | Linked Test Case |
|---|---|---|---|---|
| BUG-001 | Duplicate book names are accepted without any warning | Medium | High | TC-016 |
| BUG-002 | Book name made only of special characters produces a cryptic URL slug | Low | Medium | TC-017 |

BUG-001 is a usability concern: users can create several books with identical names and cannot tell them apart in the book listing.

## Deliverables

| Deliverable | Description | Link |
|---|---|---|
| Test Cases | 23 functional test cases with results | [manual-tests/Test-Cases.md](manual-tests/Test-Cases.md) |
| Bug Report | 2 reproducible bugs with steps and evidence | [bug-reports/Bug-Report.md](bug-reports/Bug-Report.md) |
| Automation Candidates | 3 scenarios with detailed test cases and justification | [automation/Automation-Test-Candidates.md](automation/Automation-Test-Candidates.md) |
| Test Evidence | Screenshots from test execution | [manual-tests/Evidence/](manual-tests/Evidence/) |
| Bug Evidence | Screenshots for each bug | [bug-reports/evidences/](bug-reports/evidences/) |

## Automation Candidates

| ID | Scenario | Related Test Cases |
|---|---|---|
| AUTO-001 | Create a book with valid input | TC-001, TC-002 |
| AUTO-002 | Form validation: empty name, whitespace name, XSS payload | TC-007, TC-008, TC-010 |
| AUTO-003 | Search: normal query, special characters, empty query | TC-004, TC-011, TC-022 |

The automation work is documented as detailed test cases only. The Playwright code itself is not included.

## Repository Structure

```
bookstack-qa-testing/
|-- README.md
|-- manual-tests/
|   |-- Test-Cases.md
|   |-- Book_Stack_test-cases.xlsx
|   `-- Evidence/
|-- bug-reports/
|   |-- Bug-Report.md
|   |-- Bug_report_Book_stack_demo.xlsx
|   `-- evidences/
|       |-- bug001/
|       `-- bug002/
`-- automation/
    `-- Automation-Test-Candidates.md
```

## How to Reproduce

1. Open https://demo.bookstackapp.com/
2. Log in with the public demo account (`admin@example.com` / `password`)
3. Follow the steps in [Test-Cases.md](manual-tests/Test-Cases.md) or [Bug-Report.md](bug-reports/Bug-Report.md)

## Known Limitations

- Testing was done on the public demo instance, which resets periodically, so data may not persist
- The demo may differ from a production BookStack installation
- Results were observed in Chromium and may vary in other browsers
- No API-level or database-level testing was performed (black-box testing only)
- Performance and load testing were out of scope