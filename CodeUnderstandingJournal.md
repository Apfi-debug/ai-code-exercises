# Code Understanding Journal

## Part 1: Task Creation

### Components Involved
- **models.js** → Defines the Task structure (fields like title, status).
- **storage.js** → Handles saving/retrieving tasks.
- **app.js** → Coordinates logic between CLI, models, and storage.
- **cli.js** → Accepts user input (commands to create tasks).

### Execution Flow
1. User enters a command in **cli.js** (e.g., "add task").
2. **app.js** receives the command and calls the relevant function.
3. **models.js** defines how a Task object is structured.
4. **storage.js** saves the new Task to file/memory.
5. Confirmation or task object is returned.

### Data Handling
- Tasks are stored via **storage.js** (likely in JSON file or memory).
- Each task includes fields like `title`, `status`, maybe `id`.

### Design Patterns
- Clear separation of concerns:
  - CLI for input
  - Models for structure
  - Storage for persistence
  - App for orchestration

---

## Part 2: Status Updates

### Components Involved
- **models.js** → Contains the `status` field.
- **storage.js** → Updates the stored task when status changes.
- **app.js** → Calls update logic when triggered by CLI.

### Execution Flow
1. User enters a command in **cli.js** (e.g., "mark task as done").
2. **app.js** interprets the command and calls update logic.
3. **storage.js** finds the task by ID and updates its status.
4. Updated task is returned to the user.

### Data Handling
- Status field changes from `false` → `true` (or "pending" → "completed").
- Stored back into the same file/memory.

### Design Patterns
- Consistent update flow.
- Error handling ensures invalid IDs don’t crash the program.
