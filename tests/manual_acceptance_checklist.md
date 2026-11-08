# Manual Acceptance Checklist — Browser Task Board

**Approach:** Manual acceptance testing only. No automated test framework is permitted by the single-file self-contained constraint. Run this checklist in both a 360px viewport (single-column stacking) and a desktop viewport (>= 768px, three columns).

**Environment setup:**
1. `python -m http.server 8000`
2. Open http://localhost:8000 in a browser (Chrome or Firefox recommended).
3. For 360px viewport: use browser DevTools device emulation at 360px x 640px, or a real mobile device.
4. For desktop viewport: use >= 768px width (e.g., 1280px x 800px).

---

## FR1 — Task CRUD

**Test ID:** t01_add_task
- **Title:** Create a new task with valid title and description
- **Steps:**
  1. Click the "Add task" button.
  2. Enter a title (<= 80 characters) and an optional description (<= 280 characters).
  3. Select a status (To do / In progress / Done).
  4. Click "Save".
- **Expected:** The task appears in the selected column; column count updates; title is shown as bold text; description shown below; task has a stable `task_` prefixed ID (inspect via DevTools Elements → `data-task-id`).
- **BRD criterion:** Title required, max 80; description max 280; status as text label.
- **Pass condition:** Task renders correctly with all fields and stable ID.

**Test ID:** t02_edit_task
- **Title:** Edit an existing task's title, description, and status
- **Steps:**
  1. Click "Edit" on an existing task card.
  2. Change the title, description, and status.
  3. Click "Save".
- **Expected:** Changes are reflected immediately; the task moves to the new column with updated count; the stable ID is preserved across the edit.
- **BRD criterion:** Edit capability; stable IDs across edits.
- **Pass condition:** Edited values and status are persisted and visible.

**Test ID:** t03_title_validation
- **Title:** Validate title requirements (required, max 80 characters)
- **Steps:**
  1. Open the Add task modal with an empty title; click "Save".
  2. Enter a title longer than 80 characters; click "Save".
- **Expected:** An error message appears for each violation; the task is not created.
- **BRD criterion:** Title required, max 80 chars.
- **Pass condition:** Validation errors prevent submission.

**Test ID:** t04_description_validation
- **Title:** Validate description max length (280 characters)
- **Steps:**
  1. Open the Add task modal; enter a description longer than 280 characters; click "Save".
- **Expected:** An error message appears; the task is not created.
- **BRD criterion:** Description max 280 chars.
- **Pass condition:** Validation error prevents submission.

---

## FR2 — Delete with confirmation

**Test ID:** t05_delete_confirmation
- **Title:** Delete a task only after explicit confirmation
- **Steps:**
  1. Click "Delete" on a task card.
  2. Observe the confirmation dialog.
  3. Click "Cancel".
  4. Click "Delete" again, then click "Confirm".
- **Expected:** The task is NOT removed after "Cancel"; the task IS removed after "Confirm"; the column count updates.
- **BRD criterion:** Delete with confirmation.
- **Pass condition:** Confirmation dialog blocks deletion until confirmed.

---

## FR3 — Status management

**Test ID:** t06_status_selector
- **Title:** Change task status via the labeled status selector
- **Steps:**
  1. Open a task card.
  2. Use the status dropdown to change the status (e.g., To do → In progress → Done).
- **Expected:** The task moves to the corresponding column; the column counts update live; status is shown as the text label (To do / In progress / Done).
- **BRD criterion:** Status as text labels; status management.
- **Pass condition:** Task moves columns; counts update.

**Test ID:** t07_column_counts
- **Title:** Verify column counts are accurate after every action
- **Steps:**
  1. Add tasks to each column; note counts.
  2. Move tasks between columns, delete tasks, add tasks.
- **Expected:** Each column count always reflects the actual number of visible tasks in that column.
- **BRD criterion:** Counts update with status changes.
- **Pass condition:** Counts are always correct.

---

## FR4 — Search and filter (AND logic)

**Test ID:** t08_search_and_status_filter
- **Title:** Search and status filter combine with AND logic
- **Steps:**
  1. Create at least 4 tasks with varied titles and statuses.
  2. Enter a search term that matches one task.
  3. Set the status filter to a different status.
  4. Observe results; clear filters.
- **Expected:** Results show only tasks matching BOTH the search term AND the status; the "Clear filters" button is enabled while filters are active and disabled when cleared; all tasks reappear after clearing.
- **BRD criterion:** Search and status filter combined with AND; clear filters.
- **Pass condition:** AND logic works; clear filters restores full list.

**Test ID:** t09_search_debounce
- **Title:** Search input applies after brief delay
- **Steps:**
  1. Type a search term rapidly; observe that filtering applies after typing stops.
- **Expected:** Results update after the input stabilizes (no excessive re-render per keystroke).
- **BRD criterion:** Search behavior.
- **Pass condition:** Search applies without lag.

---

## FR5 — Demo data reset

**Test ID:** t10_reset_demo_confirmation
- **Title:** Reset demo data requires confirmation and produces exactly three samples
- **Steps:**
  1. Add at least two custom tasks.
  2. Click "Reset demo".
  3. Click "Cancel"; verify custom tasks remain.
  4. Click "Reset demo" again; click "Confirm".
- **Expected:** After confirmation, exactly three tasks exist ("Set up project environment" in To do, "Implement core feature" in In progress, "Write and run tests" in Done); the confirmation dialog warns about data loss.
- **BRD criterion:** Reset demo data with confirmation; exactly three samples.
- **Pass condition:** Exactly three demo tasks after confirmation.

---

## FR6 — Empty state

**Test ID:** t11_empty_state_per_column
- **Title:** Empty state message shows per column when no tasks match
- **Steps:**
  1. Apply a filter that matches no tasks (e.g., search for a unique term).
  2. Observe each column.
  3. Clear filters; delete all tasks.
- **Expected:** Each column shows an empty-state message when it has no visible tasks; the message is hidden when tasks are present.
- **BRD criterion:** Empty state.
- **Pass condition:** Empty-state message renders when a column has no matching tasks.

---

## FR7 — Data export and import

**Test ID:** t12_export_json
- **Title:** Export tasks as valid JSON file
- **Steps:**
  1. Add several tasks.
  2. Click "Export".
  3. Open the downloaded JSON file.
- **Expected:** A JSON file downloads containing an array of task objects with stable IDs, titles, descriptions, and statuses.
- **BRD criterion:** JSON export.
- **Pass condition:** Downloaded JSON is valid and complete.

**Test ID:** t13_import_json
- **Title:** Import tasks from a JSON file
- **Steps:**
  1. Export current tasks.
  2. Modify the exported file (add a new task object with a valid ID, title, status).
  3. Click "Import"; select the modified file; click "Import".
- **Expected:** New tasks appear in the board; existing tasks with matching IDs are not duplicated.
- **BRD criterion:** JSON import.
- **Pass condition:** Imported tasks appear; no duplicate IDs.

**Test ID:** t14_invalid_import
- **Title:** Invalid JSON import is rejected gracefully
- **Steps:**
  1. Create a file with invalid JSON content.
  2. Click "Import"; select the file; click "Import".
- **Expected:** An error message is shown; no tasks are added.
- **BRD criterion:** Import validation.
- **Pass condition:** Invalid files are rejected without corrupting data.

---

## FR8 — Storage error handling

**Test ID:** t15_storage_quota_error
- **Title:** Quota exceeded error is handled gracefully
- **Steps:**
  1. Simulate localStorage quota exhaustion (e.g., set a very small quota via DevTools Application storage settings, or note the fallback behavior).
  2. Attempt to add a task.
- **Expected:** A user-friendly alert is shown; the app does not crash or lose existing tasks; existing tasks remain loadable on reload.
- **BRD criterion:** Storage error handling with graceful fallback.
- **Pass condition:** Graceful alert; no crash; existing data intact.

**Test ID:** t16_corrupted_storage_recovery
- **Title:** Corrupted localStorage is recovered gracefully on load
- **Steps:**
  1. Open DevTools Application → Local Storage; manually corrupt the `taskBoardData` value (invalid JSON).
  2. Reload the page.
- **Expected:** The app loads without crashing; an empty board is shown (or existing valid data recovered); no uncaught JavaScript errors appear in the console.
- **BRD criterion:** Storage error handling.
- **Pass condition:** App recovers without crashing.

---

## FR9 — Responsive layout

**Test ID:** t17_viewport_360px
- **Title:** Single-column stacking at 360px viewport
- **Steps:**
  1. Set viewport to 360px width (DevTools emulation or mobile device).
  2. Observe the board layout.
- **Expected:** All three columns stack vertically in a single column; the header controls wrap or stack; the search input spans full width; the layout remains usable (no horizontal scroll, no cut-off content).
- **BRD criterion:** Responsive stacking at 360px.
- **Pass condition:** Single-column layout at 360px.

**Test ID:** t18_viewport_desktop
- **Title:** Three columns on desktop (>= 768px)
- **Steps:**
  1. Set viewport to 768px or wider (e.g., 1280px).
  2. Observe the board layout.
- **Expected:** All three columns (To do, In progress, Done) are displayed side by side with equal width.
- **BRD criterion:** Three columns on desktop.
- **Pass condition:** Three-column layout at >= 768px.

**Test ID:** t19_responsive_transition
- **Title:** Layout transitions smoothly between viewports
- **Steps:**
  1. Resize the viewport from desktop to 360px and back.
- **Expected:** Columns stack and unstack smoothly; no layout breakage during resize.
- **BRD criterion:** Responsive transition.
- **Pass condition:** Smooth transition between layouts.

---

## FR10 — Stable identifiers

**Test ID:** t20_stable_ids_across_reload
- **Title:** Task IDs remain stable across page reloads
- **Steps:**
  1. Add a task; note its `task_` prefixed ID from `data-task-id`.
  2. Reload the page.
  3. Inspect the same task's `data-task-id`.
- **Expected:** The ID is identical before and after reload.
- **BRD criterion:** Stable IDs across reloads.
- **Pass condition:** ID matches after reload.

**Test ID:** t21_stable_ids_across_edit
- **Title:** Task IDs remain stable across edits
- **Steps:**
  1. Edit a task; note its `data-task-id` before and after the edit.
- **Expected:** The ID is identical before and after the edit.
- **BRD criterion:** Stable IDs across edits.
- **Pass condition:** ID matches after edit.

---

## Cross-cutting / non-functional checks

**Test ID:** t22_focus_states
- **Title:** Visible focus states on interactive elements
- **Steps:**
  1. Tab through all interactive elements (Add task, Export, Import, Reset demo, search input, status filter, task cards, Edit/Delete buttons, modal buttons).
- **Expected:** Each focused element shows a visible focus ring (blue outline); no element is skipped unexpectedly.
- **BRD criterion:** Visible focus states.
- **Pass condition:** Focus ring visible on all interactive elements.

**Test ID:** t23_keyboard_navigation
- **Title:** Keyboard accessibility (Escape closes modals)
- **Steps:**
  1. Open any modal; press the Escape key.
- **Expected:** The modal closes; focus returns to a sensible location.
- **BRD criterion:** Keyboard accessibility.
- **Pass condition:** Escape closes modals.

**Test ID:** t24_single_file_verification
- **Title:** Application runs from a single index.html with no external dependencies
- **Steps:**
  1. Inspect `index.html` in DevTools Network tab.
  2. Verify no external scripts, styles, fonts, or images are loaded.
  3. Verify there is no `package.json` in the project directory.
- **Expected:** Only `index.html` is loaded; no external resources; no `package.json`.
- **BRD criterion:** Single-file self-contained constraint.
- **Pass condition:** Zero external dependencies.

**Test ID:** t25_persistence_after_reload
- **Title:** Tasks persist in localStorage after page reload
- **Steps:**
  1. Add tasks; close the browser tab; reopen the page.
- **Expected:** All previously added tasks are present.
- **BRD criterion:** localStorage persistence.
- **Pass condition:** Tasks survive reload.

**Test ID:** t26_no_backend_http_api
- **Title:** No backend or HTTP API is used
- **Steps:**
  1. Review `index.html` source; verify all actions use inline client-side DOM event handlers.
  2. Monitor the Network tab during all interactions.
- **Expected:** No HTTP requests are made; all logic is client-side.
- **BRD criterion:** No backend; no HTTP API.
- **Pass condition:** Zero network activity.

---

## Traceability matrix

| Test ID | BRD Criterion | Component |
|---|---|---|
| t01_add_task | FR1 Task CRUD | index.html (Add modal, form submission, validation) |
| t02_edit_task | FR1 Task CRUD | index.html (Edit modal, updateTask) |
| t03_title_validation | FR1 Task CRUD | index.html (validateTask) |
| t04_description_validation | FR1 Task CRUD | index.html (validateTask) |
| t05_delete_confirmation | FR2 Delete with confirmation | index.html (confirmModal, requestDelete) |
| t06_status_selector | FR3 Status management | index.html (status selector, updateTaskStatus) |
| t07_column_counts | FR3 Status management | index.html (render, count updates) |
| t08_search_and_status_filter | FR4 Search and filter | index.html (filters, AND logic) |
| t09_search_debounce | FR4 Search and filter | index.html (debounced search) |
| t10_reset_demo_confirmation | FR5 Demo data reset | index.html (resetDemoData) |
| t11_empty_state_per_column | FR6 Empty state | index.html (empty-state rendering) |
| t12_export_json | FR7 Data export and import | index.html (exportTasks) |
| t13_import_json | FR7 Data export and import | index.html (confirmImport) |
| t14_invalid_import | FR7 Data export and import | index.html (import validation) |
| t15_storage_quota_error | FR8 Storage error handling | index.html (storage.save error handling) |
| t16_corrupted_storage_recovery | FR8 Storage error handling | index.html (storage.load error handling) |
| t17_viewport_360px | FR9 Responsive layout | index.html (CSS media queries) |
| t18_viewport_desktop | FR9 Responsive layout | index.html (CSS grid) |
| t19_responsive_transition | FR9 Responsive layout | index.html (media query transition) |
| t20_stable_ids_across_reload | FR10 Stable identifiers | index.html (generateId, localStorage) |
| t21_stable_ids_across_edit | FR10 Stable identifiers | index.html (edit preserves ID) |
| t22_focus_states | Cross-cutting | index.html (focus styles) |
| t23_keyboard_navigation | Cross-cutting | index.html (Escape key handler) |
| t24_single_file_verification | Cross-cutting | index.html, project structure |
| t25_persistence_after_reload | Cross-cutting | index.html (localStorage) |
| t26_no_backend_http_api | Cross-cutting | index.html (no HTTP calls) |

## Execution notes

- Run the full checklist on **both** viewports (360px and desktop).
- Record pass/fail per test ID; note any deviations.
- All functional behavior is implemented via inline client-side DOM event handlers in `index.html`; no backend or HTTP API is involved.
