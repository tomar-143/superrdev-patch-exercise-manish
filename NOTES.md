# Patch Exercise Notes

## Fixes Made

### 1. Status filter returned incorrect results

- **Issue:** Selecting a status could still return tasks with other statuses.
- **Found:** Tested `/api/tasks?status=OPEN` directly and compared the response with the UI.
- **Root cause:** SQL `AND`/`OR` precedence caused the status condition to apply only to part of the search condition.
- **Fix:** Added parentheses around the title/description search condition in `TaskRepository.java`.
- **Why:** Ensures the status filter applies to the complete search condition.

### 2. Search/status changes did not reset pagination

- **Issue:** Changing the search or status while on page 2 could show an unexpected page.
- **Found:** Navigated to page 2 and then changed the filter.
- **Root cause:** `App.jsx` updated the query/status without resetting the page.
- **Fix:** Reset page to 1 whenever search or status changes.
- **Why:** Filtered results should always start from the first page.

### 3. Invalid pagination caused HTTP 500

- **Issue:** `page=0` or negative pagination values caused a server error.
- **Found:** Tested the API directly.
- **Root cause:** Negative indexes were passed to `subList()`.
- **Fix:** Validate `page` and `pageSize` and return HTTP 400 for invalid values.
- **Why:** Invalid client input should produce a clear client error instead of a server exception.

### 4. Invalid status caused HTTP 500

- **Issue:** An unknown status caused an unhandled exception.
- **Found:** Tested `/api/tasks?status=INVALID`.
- **Root cause:** `TaskStatus.valueOf()` throws `IllegalArgumentException` for unknown values.
- **Fix:** Catch the exception and return HTTP 400 with an error message.
- **Why:** Invalid request parameters should be handled gracefully.

## Not Changed

I did not refactor unrelated code or remove the existing artificial search delay because the exercise is time-boxed and the focus was on reproducible functional and API correctness issues.

## Biggest Remaining Risk

The backend still performs search and pagination in application memory and contains an artificial search delay. This could become a performance concern with a larger dataset.

## AI / Tools Used

Used AI assistance for debugging, identifying likely root causes, and reviewing fixes. Verified all changes manually through the application and API using the browser and local development environment.
