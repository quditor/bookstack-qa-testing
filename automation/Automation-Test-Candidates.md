# Automation Test Candidates — BookStack Demo

**Project:** BookStack Demo – QA Testing Project  
**Application URL:** https://demo.bookstackapp.com/  
**Proposed Framework:** Playwright (JavaScript)  
**Date:** 2026-10-27  
**Source:** Derived from the executed functional test suite (`manual-tests/Test-Cases.md`)  

---

## Automation Candidate #1: Book Creation — Valid Input (Smoke / Regression)

| Field | Details |
|---|---|
| **Automation Test ID** | AUTO-001 |
| **Scenario Name** | Verify successful creation of a book with a valid name and description |
| **Corresponding Manual Test Case** | TC-001 (Create a book with a valid name), TC-002 (Create a book with name and description) |

### Objective

Validate that the most critical workflow in BookStack — creating a new book with valid input — functions correctly end-to-end. This covers form rendering, data submission, redirect behavior, persisted display, and listing presence. A failure here blocks all downstream content creation (chapters, pages).

### Preconditions

1. BookStack Demo is accessible at `https://demo.bookstackapp.com/`
2. The public demo admin account is available (`admin@example.com` / `password`)
3. The user has permission to create and delete books
4. No pre-existing book with the exact generated test name exists (enforced by unique timestamp suffix)

### Test Data

| Field | Value | Notes |
|---|---|---|
| Login Email | `admin@example.com` | Public demo login |
| Login Password | `password` | Public demo login |
| Book Name | `Auto Test Book - {Date.now()}` | Unique per execution to avoid slug collisions |
| Book Description | `Automated regression test book created at {ISO timestamp}` | Plain text |

### Detailed Test Steps

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Navigate to login page | `https://demo.bookstackapp.com/login` |
| 2 | Enter email into the email field | `input[name="email"]` |
| 3 | Enter password into the password field | `input[name="password"]` |
| 4 | Click the "Log In" button | `button:has-text("Log In")` or `input[type="submit"]` |
| 5 | **Assert** — Login succeeds | URL no longer contains `/login` |
| 6 | Navigate to the create book page | `https://demo.bookstackapp.com/create-book` |
| 7 | **Assert** — Create book form loads | `input[name="name"]` is visible |
| 8 | Enter the book name | `input[name="name"]` → fill with generated name |
| 9 | Enter text into the Description editor | Target the editor's contenteditable element or iframe; type plain text |
| 10 | Click "Save Book" | `button:has-text("Save Book")` |
| 11 | **Assert** — Redirect to new book page | URL matches `/books/{slug}` |
| 12 | **Assert** — Page heading shows the book name | `h1` text matches entered name |
| 13 | **Assert** — Description text is visible | Page body contains the entered description |
| 14 | Navigate to books listing | `https://demo.bookstackapp.com/books` |
| 15 | **Assert** — New book appears in the listing | An element containing the book name is visible |
| 16 | **Cleanup** — Delete the created book | Open book → Delete → Confirm |

### Expected Results

| # | Expected Outcome |
|---|---|
| 1 | Login completes without error; user is authenticated |
| 2 | Create book form renders with Name and Description fields |
| 3 | After saving, the browser redirects to the new book's page |
| 4 | The book page heading exactly matches the entered name |
| 5 | The description text is visible on the book page |
| 6 | The book is listed on the `/books` page |
| 7 | Cleanup successfully removes the book |

### Assertions to Implement

| # | Assertion | Type | Playwright API |
|---|---|---|---|
| 1 | URL does not contain `/login` after login | URL | `await expect(page).not.toHaveURL(/login/)` |
| 2 | Name input is visible on create form | Visibility | `await expect(nameInput).toBeVisible()` |
| 3 | URL matches `/books/` pattern after save | URL | `await expect(page).toHaveURL(/\/books\//)` |
| 4 | `h1` text matches entered book name | Text | `await expect(page.locator('h1')).toHaveText(bookName)` |
| 5 | Description text exists on page | Text | `await expect(page.locator('body')).toContainText(description)` |
| 6 | Book name appears in listing | Visibility | `await expect(page.getByText(bookName).first()).toBeVisible()` |

### Why Automation Is Appropriate

| Justification | Explanation |
|---|---|
| **Critical-path regression** | Book creation is the foundational workflow. If it breaks, no content can be added. Running it on every deployment catches regressions within seconds. |
| **High execution frequency** | The test should run on every build and merge. Doing this manually takes 3–5 minutes each time and is error-prone. |
| **Stable and deterministic** | Standard form fields, a predictable redirect, and clear success indicators make the test reliable. |

### Dependencies and Limitations

| Dependency / Limitation | Impact |
|---|---|
| Network access to the demo instance | Test fails if the demo site is down |
| Demo instance resets periodically | Cleanup may find data already deleted |
| Rich text description editor | May require targeting an iframe or contenteditable element instead of a `<textarea>` |

---

## Automation Candidate #2: Form Validation — Empty Name, Whitespace, and XSS Payload

| Field | Details |
|---|---|
| **Automation Test ID** | AUTO-002 |
| **Scenario Name** | Verify that the book creation form rejects empty/whitespace names and safely handles XSS payloads |
| **Corresponding Manual Test Case** | TC-007 (Empty name), TC-008 (Whitespace-only name), TC-010 (XSS payload in name) |

### Objective

Validate that the book creation form enforces validation for empty and whitespace-only names, and that cross-site scripting (XSS) payloads entered in the name field are sanitized and never executed as JavaScript. This protects data integrity and application security.

### Preconditions

1. BookStack Demo is accessible at `https://demo.bookstackapp.com/`
2. The public demo admin account is available (`admin@example.com` / `password`)
3. User is authenticated as Admin (login performed in a `beforeEach` hook)

### Test Data

| Sub-Scenario | Input for Name Field | Expected Behavior |
|---|---|---|
| **A — Empty Name** | `""` (field left blank) | Validation error: *"The name field is required."* |
| **B — Whitespace Only** | `"     "` (five spaces) | Validation error: *"The name field is required."* |
| **C — XSS Payload** | `<script>alert('XSS')</script>` | Book created; script tags shown as **escaped plain text**; no alert dialog |

### Detailed Test Steps

**Sub-scenario A — Empty Name:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Navigate to `/create-book` | Direct URL navigation |
| 2 | Leave the Name field empty | Do not interact with `input[name="name"]` |
| 3 | Click "Save Book" | `button:has-text("Save Book")` |
| 4 | **Assert** — Validation error is displayed | Error element is visible |
| 5 | **Assert** — Error text includes "The name field is required" | Text content assertion |
| 6 | **Assert** — URL remains on `/create-book` | No redirect occurred |

**Sub-scenario B — Whitespace-Only Name:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Navigate to `/create-book` | Direct URL navigation |
| 2 | Enter five spaces in the Name field | `input[name="name"]` → fill with `"     "` |
| 3 | Click "Save Book" | `button:has-text("Save Book")` |
| 4 | **Assert** — Validation error is displayed | Same error element as sub-scenario A |
| 5 | **Assert** — Error text includes "The name field is required" | Server trimmed whitespace and rejected |

**Sub-scenario C — XSS Payload:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Register a dialog listener before navigating | `page.on('dialog', ...)` increments a counter |
| 2 | Navigate to `/create-book` | Direct URL navigation |
| 3 | Enter `<script>alert('XSS')</script>` in the Name field | `input[name="name"]` |
| 4 | Click "Save Book" | `button:has-text("Save Book")` |
| 5 | **Assert** — Book is created (redirect occurs) | URL matches `/books/` pattern |
| 6 | **Assert** — Heading displays the XSS string as plain text | `h1` contains the literal text `<script>alert('XSS')</script>` |
| 7 | **Assert** — No JavaScript alert dialog appeared | Dialog counter equals 0 |
| 8 | **Assert** — Heading HTML is escaped | `innerHTML` of heading contains `&lt;script&gt;` |
| 9 | **Cleanup** — Delete the created book | Open book → Delete → Confirm |

### Expected Results

| # | Expected Outcome |
|---|---|
| 1 | Empty name submission produces a validation error, not a book |
| 2 | Whitespace-only name is treated as empty and rejected |
| 3 | URL remains on `/create-book` when validation fails |
| 4 | XSS payload is stored and displayed as **escaped text**, never executed |
| 5 | No JavaScript dialog appears during or after XSS submission |

### Assertions to Implement

| # | Assertion | Type | Playwright API |
|---|---|---|---|
| 1 | Validation error element is visible | Visibility | `await expect(errorEl).toBeVisible()` |
| 2 | Error text contains "The name field is required" | Text | `await expect(errorEl).toContainText('The name field is required')` |
| 3 | URL remains `/create-book` after failed submission | URL | `await expect(page).toHaveURL(/create-book/)` |
| 4 | XSS string displayed as plain text in heading | Text | `await expect(heading).toContainText('<script>')` |
| 5 | No dialog event fired | Event Listener | Dialog counter: `expect(dialogCount).toBe(0)` |
| 6 | Heading innerHTML uses escaped entities | DOM Property | `expect(await heading.innerHTML()).toContain('&lt;script&gt;')` |

### Why Automation Is Appropriate

| Justification | Explanation |
|---|---|
| **Security regression prevention** | XSS protection can be broken by refactoring or dependency updates. An automated test gives continuous, low-cost verification. |
| **Validation consistency** | If a code change weakens the empty-name check, this test catches it immediately. |
| **Multi-scenario efficiency** | Three negative scenarios run in about 5 seconds automatically, versus 5+ minutes manually. |

### Dependencies and Limitations

| Dependency / Limitation | Impact |
|---|---|
| Validation message text may change | Use `toContainText` rather than exact string matching |
| Client-side validation may be added later | Browser may block submission before the server check; the test would need adjusting |
| XSS sub-scenario creates a book | Use an `afterEach` hook so cleanup still runs on failure |

---

## Automation Candidate #3: Search Functionality — Normal Query, Special Characters, and Empty Query

| Field | Details |
|---|---|
| **Automation Test ID** | AUTO-003 |
| **Scenario Name** | Verify that search returns correct results for normal queries, safely handles special characters, and gracefully handles empty queries |
| **Corresponding Manual Test Case** | TC-004 (Search for a book by name), TC-011 (Search with special characters), TC-022 (Empty search query) |

### Objective

Validate that search retrieves books by name, safely handles HTML/special characters in the query, and handles an empty query without errors. Search is the primary discovery mechanism in a knowledge base, so its reliability directly affects users.

### Preconditions

1. BookStack Demo is accessible at `https://demo.bookstackapp.com/`
2. The public demo admin account is available (`admin@example.com` / `password`)
3. User is authenticated as Admin
4. **Setup:** A book with a known, unique name is created before the search tests run, so at least one match is guaranteed

### Test Data

| Sub-Scenario | Search Query | Setup Required | Expected Result |
|---|---|---|---|
| **A — Normal Search** | `Search Target {timestamp}` | Create a book with this exact name first | At least one result containing the book name |
| **B — Special Character Search** | `<script>alert('test')</script>` | None | Page loads safely; no script execution; query shown as escaped text |
| **C — Empty Search** | `""` (empty string) | None | Page loads without a server error (status below 500) |

### Detailed Test Steps

**Setup (runs once before all sub-scenarios):**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Log in as Admin | Standard login flow |
| 2 | Navigate to `/create-book` | Direct URL |
| 3 | Create a book named `Search Target {timestamp}` | `input[name="name"]` → Save |
| 4 | **Assert** — Book created | URL matches `/books/` |

**Sub-scenario A — Normal Search:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Navigate to `/search?term=Search+Target+{timestamp}` | Direct URL, or use the search bar UI |
| 2 | **Assert** — Search results page loads | URL contains `/search` |
| 3 | **Assert** — Results contain the test book name | First matching result is visible |

**Sub-scenario B — Special Character Search:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Register a dialog listener | `page.on('dialog', ...)` increments a counter |
| 2 | Enter `<script>alert('test')</script>` in the search input | Top navigation search bar |
| 3 | Submit the search | Press Enter |
| 4 | **Assert** — Page renders without server error | Response status below 500 |
| 5 | **Assert** — No JavaScript alert dialog appears | Dialog counter equals 0 |
| 6 | **Assert** — Query is displayed safely | Page text contains the literal `<script>` as escaped text |

**Sub-scenario C — Empty Search:**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Navigate to `/search?term=` | Direct URL; capture the response |
| 2 | **Assert** — Response status is below 500 | No server error |
| 3 | **Assert** — No uncaught JavaScript error | `page.on('pageerror')` counter equals 0 |

**Cleanup (runs once after all sub-scenarios):**

| Step | Action | Locator / Detail |
|---|---|---|
| 1 | Delete the `Search Target {timestamp}` book | Open book → Delete → Confirm |

### Expected Results

| # | Expected Outcome |
|---|---|
| 1 | Normal search returns results that include the known test book |
| 2 | Special character search does not trigger script execution |
| 3 | Special characters in the query are displayed as escaped text |
| 4 | Empty search does not cause a server error |
| 5 | No uncaught JavaScript errors in any scenario |

### Assertions to Implement

| # | Assertion | Type | Playwright API |
|---|---|---|---|
| 1 | Search results page loads | URL | `await expect(page).toHaveURL(/search/)` |
| 2 | At least one result contains the book name | Visibility | `await expect(page.locator('.entity-list-item', { hasText: bookName }).first()).toBeVisible()` |
| 3 | No dialog fired during special character search | Event Listener | `expect(dialogCount).toBe(0)` |
| 4 | Query displayed as escaped text | Text | `await expect(page.locator('body')).toContainText('<script>')` |
| 5 | Empty search returns no server error | Response Status | `const res = await page.goto('/search?term='); expect(res.status()).toBeLessThan(500);` |
| 6 | No uncaught page errors | Event Listener | `expect(pageErrorCount).toBe(0)` |

### Why Automation Is Appropriate

| Justification | Explanation |
|---|---|
| **Cross-module regression coverage** | Search draws on books, chapters, and pages. A change in any module can break indexing or rendering, which is easy to miss manually. |
| **Security at the input boundary** | Search boxes are a common XSS vector. An automated check keeps sanitization intact across updates. |
| **Deterministic data** | The test creates its own known book, searches for it, and cleans up, so it does not depend on the demo's current state. |

### Dependencies and Limitations

| Dependency / Limitation | Impact |
|---|---|
| Search indexing delay | A new book may not be searchable instantly; use Playwright's auto-retrying `expect` or a short retry |
| Demo instance variability | Other users' books may also match; assert that the first match is visible rather than an exact count |
| Result selectors | `.entity-list-item` must be verified against the actual DOM, as it may change between versions |

---

## Summary

| ID | Scenario | Test Type | Manual TC | Key Assertion | Automation Value |
|---|---|---|---|---|---|
| **AUTO-001** | Book Creation (Valid Input) | Smoke / Regression | TC-001, TC-002 | Book created, redirected, listed | Core workflow; blocks all content creation if broken |
| **AUTO-002** | Form Validation (Empty, Whitespace, XSS) | Negative / Security | TC-007, TC-008, TC-010 | Validation error shown; XSS escaped | Security regression; data integrity protection |
| **AUTO-003** | Search (Normal, Special Chars, Empty) | Functional / Security | TC-004, TC-011, TC-022 | Results found; XSS safe; no errors | Cross-module coverage; XSS at input boundary |

### Recommended Execution Strategy

```
Trigger:       Every deployment, PR merge, or nightly CI build
Framework:     Playwright (JavaScript)
Browser:       Chromium (headless for CI, headed for debugging)
Parallelism:   Sequential (shared demo instance, avoid data contention)
Credentials:   Public demo login (admin@example.com / password)
Cleanup:       afterEach / afterAll hooks delete all test-created data
Reporting:     Playwright HTML reporter
```

---

*End of Automation Test Candidates*