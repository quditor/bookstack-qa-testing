# Functional Test Cases & Execution Results

## 1. Test Overview

| Item | Details |
|---|---|
| Application Under Test | BookStack Demo (https://demo.bookstackapp.com/) |
| Selected Workflow | **Book Management** (Create, Edit, Delete, Search) |
| Tester | _Your Name_ |
| Test Date | 2026-10-27 |
| Browser | Chrome |
| Login Used | admin@example.com (Admin role) |
| Test Types Covered | Positive, Negative, Boundary Value, Edge Case |

## 2. Execution Summary

| Metric | Count |
|---|---|
| Total Test Cases | 23 |
| ✅ Passed | 22 |
| ❌ Failed | 1 |
| Blocked | 0 |
| Not Executed | 0 |

**By test type**

| Test Type | Count | IDs |
|---|---|---|
| Positive Testing | 6 | TC-001 – TC-006 |
| Negative Testing | 5 | TC-007 – TC-011 |
| Boundary Value Testing | 4 | TC-012 – TC-015 |
| Edge Case Testing | 8 | TC-016 – TC-023 |

**By severity**

| Severity | Count |
|---|---|
| High | 7 |
| Medium | 11 |
| Low | 5 |

**Bugs found**

| Bug ID | Title | Severity | Priority | Linked Test Case |
|---|---|---|---|---|
| BUG-001 | Duplicate book names accepted without warning | Medium | High | TC-016 |
| BUG-002 | Cryptic URL slug for special-character-only book names | Low | Low | TC-017 |

Full details are in [Bug-Report.md](../bug-reports/Bug-Report.md).

## 3. Test Cases

### 3.1 Positive Testing

| ID | Test Title | Preconditions | Test Data | Steps to Execute | Expected Result | Actual Result | Status | Severity / Priority |
|---|---|---|---|---|---|---|---|---|
| TC-001 | Verify create a book with a valid name | User is logged in as Admin | Book Name: SQA Test Book Alpha | 1. Navigate to /create-book<br>2. Enter SQA Test Book Alpha in Name<br>3. Click Save Book | Book is created successfully and user is redirected to the new book page | Book created successfully. Redirected to /books/sqa-test-book-alpha. Title displayed correctly. | ✅ PASS | Medium / Medium |
| TC-002 | Verify create a book with name and description | User is logged in as Admin | Name: SQA Description Test<br>Description: Formatted content | 1. Navigate to /create-book<br>2. Enter name and description<br>3. Click Save Book | Book is created with both name and description displayed on the book page | Book created successfully. Description rendered with formatted HTML content. | ✅ PASS | Low / Low |
| TC-003 | Verify edit an existing book name | User is logged in as Admin.<br>Book exists. | New Name: SQA Test Book Alpha - Edited | 1. Navigate to the book<br>2. Click Edit<br>3. Change name<br>4. Click Save Book | Book name is updated. User redirected to book page with new name. | Book name updated successfully. URL slug changed. Redirected to updated book page. | ✅ PASS | Medium / Medium |
| TC-004 | Verify Search for a book by name | User is logged in as Admin.<br>Test books exist. | Search query: SQA Test Book | 1. Click search icon<br>2. Enter SQA Test Book<br>3. Press Enter | Search results display books matching the query | Search results displayed matching books with titles and descriptions. | ✅ PASS | Medium / Medium |
| TC-005 | Verify create a book with numeric-only name | User is logged in as Admin | Book Name: 12345 | 1. Navigate to /create-book<br>2. Enter 12345 in Name<br>3. Click Save Book | Book is created successfully with numeric name | Book created successfully. URL slug generated as numeric-based slug. | ✅ PASS | Low / Low |
| TC-006 | Verify create a page inside a book | User is logged in as Admin.<br>A test book exists. | Page Name: Test Page Alpha | 1. Navigate to a test book<br>2. Click New Page<br>3. Enter page name<br>4. Add body content<br>5. Click Save Page | Page is created and visible in the book page list | Page created successfully. Visible in the book table of contents. | ✅ PASS | Medium / Medium |

### 3.2 Negative Testing

| ID | Test Title | Preconditions | Test Data | Steps to Execute | Expected Result | Actual Result | Status | Severity / Priority |
|---|---|---|---|---|---|---|---|---|
| TC-007 | Verify submit book creation with empty name | User is logged in as Admin | Book Name: (empty) | 1. Navigate to /create-book<br>2. Leave Name field empty<br>3. Click Save Book | Validation error shown. Book is not created. | Validation error: The name field is required. Book was not created. | ✅ PASS | High / High |
| TC-008 | Verify submit with whitespace-only name | User is logged in as Admin | Book Name: (5 spaces) | 1. Navigate to /create-book<br>2. Enter only spaces in Name<br>3. Click Save Book | Validation error shown. Whitespace-only name rejected. | Validation error: The name field is required. Whitespace trimmed and rejected. | ✅ PASS | High / High |
| TC-009 | Verify edit form - clear name to empty and save | User is logged in as Admin.<br>Existing book being edited. | Name: (cleared to empty) | 1. Navigate to existing book<br>2. Click Edit<br>3. Clear Name field<br>4. Click Save Book | Validation error shown. Name cannot be empty. | Validation error: The name field is required. Edit rejected; book retained previous name. | ✅ PASS | High / High |
| TC-010 | Verify create book with XSS payload in name | User is logged in as Admin | Book Name: &lt;script&gt;alert(XSS)&lt;/script&gt; | 1. Navigate to /create-book<br>2. Enter XSS payload in Name<br>3. Click Save Book | XSS payload sanitized/escaped. No script execution. | Book created. Script tags rendered as plain text. No JS alert executed. | ✅ PASS | High / High |
| TC-011 | Verify search with HTML/special character payload | User is logged in as Admin | Search query: &lt;script&gt; | 1. Click search icon<br>2. Enter &lt;script&gt; in search bar<br>3. Press Enter | Search handles special characters safely. No script execution. | Search executed safely. XSS book appeared in results. No script execution. | ✅ PASS | High / High |

### 3.3 Boundary Value Testing

| ID | Test Title | Preconditions | Test Data | Steps to Execute | Expected Result | Actual Result | Status | Severity / Priority |
|---|---|---|---|---|---|---|---|---|
| TC-012 | Verify create book with minimum valid name (1 char) | User is logged in as Admin | Book Name: A (single character) | 1. Navigate to /create-book<br>2. Enter A in Name<br>3. Click Save Book | Book created successfully with single-character name | Book created with single character A. URL slug generated appropriately. | ✅ PASS | Medium / Medium |
| TC-013 | Verify create book with very long name (250 chars) | User is logged in as Admin | Book Name: A repeated 250 times | 1. Navigate to /create-book<br>2. Enter 250 A characters in Name<br>3. Click Save Book | Book created (if no max limit) or validation message shown | Book created with 250-char name. No max length validation error displayed. | ✅ PASS | Medium / Medium |
| TC-014 | Verify create page with very long name (200+ chars) | User is logged in as Admin.<br>Test book exists. | Page Name: B repeated 200 times | 1. Navigate to test book<br>2. Click New Page<br>3. Enter 200 B chars as page name<br>4. Click Save Page | Page created (if no limit) or validation message shown | Page created with very long name. Full name displayed. | ✅ PASS | Medium / Medium |
| TC-015 | Verify create page with empty body content | User is logged in as Admin.<br>Test book exists. | Page Name: Test Page Alpha<br>Body: (empty) | 1. Navigate to test book<br>2. Click New Page<br>3. Enter page name<br>4. Leave body empty<br>5. Click Save Page | Page created with empty body or warning shown | Page created with empty body. Draft auto-saved. | ✅ PASS | Low / Low |

### 3.4 Edge Case Testing

| ID | Test Title | Preconditions | Test Data | Steps to Execute | Expected Result | Actual Result | Status | Severity / Priority |
|---|---|---|---|---|---|---|---|---|
| TC-016 | Verify create book with duplicate name | User is logged in as Admin.<br>Book Duplicate Test Book exists. | Book Name: Duplicate Test Book (same as existing) | 1. Navigate to /create-book<br>2. Enter same name as existing book<br>3. Click Save Book<br>4. Navigate to /books | Duplicate rejected with warning, or clear differentiation shown | Book created without any warning. Different URL slug generated. Both books show same display name. No warning presented. | ❌ FAIL | Medium / High |
| TC-017 | Verify create book with special characters only | User is logged in as Admin | Book Name: !@#$%^&*() | 1. Navigate to /create-book<br>2. Enter !@#$%^&*() in Name<br>3. Click Save Book<br>4. Observe URL slug | Book created. URL slug handles special chars meaningfully. | Book created. URL slug was cryptic and non-descriptive (e.g. at-D26). | ✅ PASS | Low / Low |
| TC-018 | Verify create book with Unicode/Emoji characters | User is logged in as Admin | Book Name: Test Book with emoji and Japanese/Arabic | 1. Navigate to /create-book<br>2. Enter Unicode/emoji name<br>3. Click Save Book | Book created. Unicode preserved in display name. | Book created. Unicode and emoji preserved in display name. | ✅ PASS | Medium / Low |
| TC-019 | Verify create book with name matching URL path | User is logged in as Admin | Book Name: create-book | 1. Navigate to /create-book<br>2. Enter create-book as Name<br>3. Click Save Book<br>4. Verify routing works | Book created without routing conflicts | Book created. System handled URL collision correctly. Routing continued to function. | ✅ PASS | High / High |
| TC-020 | Verify browser back button after form submission | User is logged in as Admin | Book Name: Back Button Test | 1. Navigate to /create-book<br>2. Enter name<br>3. Click Save Book<br>4. Click browser Back button<br>5. Observe result | Form shown again. No duplicate created. Form empty or cache warning. | Back button returned to create form. Form appeared empty. No duplicate created. | ✅ PASS | Medium / Medium |
| TC-021 | Verify delete book - confirmation flow | User is logged in as Admin.<br>Test book exists. | Target: Any test book | 1. Navigate to test book<br>2. Click Delete option<br>3. Observe confirmation dialog<br>4. Note warning message | Confirmation dialog appears. Accidental deletion prevented. | Delete confirmation dialog with clear warning and Confirm button. Explicit action required. | ✅ PASS | High / High |
| TC-022 | Verify empty search query submission | User is logged in as Admin | Search query: (empty) | 1. Click search icon/bar<br>2. Submit empty search<br>3. Observe result | Search not submitted, or appropriate message shown, or all results returned | Empty search submitted. Application displayed search page gracefully. No error. | ✅ PASS | Low / Low |
| TC-023 | Verify book sort functionality on listing page | User is logged in as Admin.<br>Multiple books exist. | Sort by: Name, Date | 1. Navigate to /books<br>2. Locate sort controls<br>3. Try different sort criteria<br>4. Observe list order | Books sorted according to selected criteria | Sort controls available. Sorting by name and date worked correctly. | ✅ PASS | Medium / Medium |

## 4. Evidence & Remarks

| ID | Evidence (screenshots) | Remarks |
|---|---|---|
| TC-001 | `screenshots/filled_form_alpha.png`<br>`screenshots/book_success_alpha.png` | URL slug auto-generated from book name |
| TC-002 | `screenshots/description_html_test.png` | Description uses WYSIWYG rich text editor |
| TC-003 | `screenshots/book_edited_success.png` | URL slug updates when name changes |
| TC-004 | `screenshots/search_results_normal.png` | Search works across book names and content |
| TC-005 | `screenshots/test_b_numeric_book.png` | Handles purely numeric names gracefully |
| TC-006 | `screenshots/test_c_create_page.png` |  |
| TC-007 | `screenshots/empty_name_validation_error.png` | Server-side validation prevents empty name |
| TC-008 | `screenshots/whitespace_validation_error.png` | Server trims whitespace and validates |
| TC-009 | `screenshots/edit_name_validation_error.png` | Edit form enforces same validation as create |
| TC-010 | `screenshots/xss_test_result.png` | XSS properly handled - tags escaped |
| TC-011 | `screenshots/search_results_special.png` | Search output escapes HTML entities |
| TC-012 | `screenshots/single_character_A.png` | Minimum boundary - single char accepted |
| TC-013 | `screenshots/long_string_result.png` | No visible max length limit on book name |
| TC-014 | `screenshots/test_c_long_name_saved.png` | No max length enforcement on page name |
| TC-015 | `screenshots/test_c_empty_page_saved.png` | Empty body pages allowed - design decision |
| TC-016 | `screenshots/test_a_book_1.png`<br>`screenshots/test_a_book_2.png`<br>`screenshots/test_a_books_list.png` | BUG-001: No duplicate name warning. See Bug Report. |
| TC-017 | `screenshots/special_chars_book.png` | BUG-002: Slug not user-friendly. See Bug Report. |
| TC-018 | `screenshots/unicode_book.png` | Good internationalization support |
| TC-019 | `screenshots/create-book.png` | Good URL collision prevention |
| TC-020 | `screenshots/test_d_back_button_result.png` | PRG pattern prevents double submission |
| TC-021 | `screenshots/delete_confirmation_dialog.png` | Good UX - requires explicit confirmation |
| TC-022 | `screenshots/search_results.png` | Graceful handling of empty search |
| TC-023 | `screenshots/sort_controls.png` | Sort functionality works as expected |

## 5. Conclusion

Book Management was tested with 23 test cases. 22 passed and 1 failed. Core create, edit, search, delete-confirmation, validation and XSS-handling behaviour works as expected. The one failed case (TC-016) revealed that duplicate book names are accepted with no warning (BUG-001). TC-017 passed functionally but exposed a usability issue with auto-generated URL slugs (BUG-002).
