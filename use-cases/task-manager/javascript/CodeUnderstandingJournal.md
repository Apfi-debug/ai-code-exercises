# Code Understanding Journal

## Overview
This task manager is organized around four main files:
- `cli.js`: command-line interface for user input and output.
- `app.js`: orchestration layer that executes business rules.
- `models.js`: task structure and task-related behavior.
- `storage.js`: persistence layer for saving, loading, and querying tasks.

## Components
- `cli.js` parses user commands with `commander` and forwards them to `TaskManager`.
- `app.js` exposes methods for creating tasks, listing tasks, updating status/priority/due date, tagging, deleting, and statistics.
- `models.js` defines `Task`, `TaskPriority`, and `TaskStatus`, and includes methods like `update`, `markAsDone`, and `isOverdue`.
- `storage.js` reads/writes `tasks.json`, restores saved tasks, and provides query methods like `getTasksByStatus`, `getTasksByPriority`, and `getOverdueTasks`.

## Execution Flow
1. User runs a command from the terminal, e.g. `node cli.js create "Buy milk" -p 3 -u 2026-06-20`.
2. `cli.js` parses the command and arguments.
3. `cli.js` calls the appropriate `TaskManager` method in `app.js`.
4. `app.js` uses `models.js` to build or update tasks and uses `storage.js` to persist them.
5. `storage.js` saves updated task data to `tasks.json`.
6. `cli.js` prints confirmation or results back to the user.

## Data Handling
- Tasks are JavaScript objects with fields: `id`, `title`, `description`, `priority`, `status`, `createdAt`, `updatedAt`, `dueDate`, `completedAt`, and `tags`.
- `Task` objects are created with a UUID and default status `todo`.
- Persistence is handled by `storage.js` via JSON file storage.
- When loading tasks, `storage.js` restores dates and other properties into new `Task` instances.
- Filtering and selection is performed in `storage.js`, while `app.js` decides which query to use.

## Supported Features
- Create tasks with title, description, priority, due date, and tags.
- List all tasks or filter by status, priority, and overdue state.
- Update task status, priority, and due date.
- Add or remove tags from tasks.
- Show details for a single task.
- Delete tasks.
- View statistics for total tasks, status counts, priority counts, overdue tasks, and tasks completed in the last 7 days.

## Design Patterns
- Separation of concerns: input (`cli.js`), orchestration (`app.js`), data model (`models.js`), persistence (`storage.js`).
- Encapsulation: `Task` includes its own update and completion behavior.
- Orchestration pattern: `TaskManager` centralizes operations and delegates to storage or model behavior.
- Persistence pattern: tasks are saved to disk as JSON and rehydrated on startup.

## Notes
- The previous journal duplicated sections and repeated the same flows multiple times.
- This version captures the implementation once, without duplicate Part 1/Part 2/Part 3 sections.
- It now matches the actual code structure and supported CLI commands more closely.

# Exercise 2
## Guided questions
My approach so far:
- I've searched for keywords like "priority", "taskPriority", "sort"
- I looked in the `models`, `app`, and `cli` directories which seemed relevant
- I think the feature might relate to how tasks are created in `models.js` and displayed in `app.js`

Project structure:
- models.js
- storage.js
- app.js
- cli.js

Based on my search, these files might be relevant, but I'm not sure:
- models.js (Task class)
- app.js (task listing logic)

## Part 2: Task Prioritization

### Initial Understanding
- I saw a `priority` field in `models.js` when tasks are created.
- I initially assumed it was text-based (High, Medium, Low).

### What Guided Questions Revealed
- Priority is passed from CLI input into `app.js` when creating tasks.
- `app.js` uses priority for filtering (it can return tasks by priority when requested), rather than automatically sorting all lists by priority.
- `storage.js` saves priority along with other task fields.
- Priority can be updated by editing a task, not just at creation.

### Key Insights
- Priority is functional, not decorative — it is used to filter and classify tasks.
- CLI commands allow users to set priority at creation.
- `app.js` supports filtering task lists by priority, rather than automatically sorting all tasks by priority.

### Misconceptions Clarified
- I thought priority was just a label.  
- Guided questions showed it’s actively used in task listing and updates.

### What I Discovered

- **Priority setting** → The `priority` field is defined in `models.js` when a task is created. It accepts numeric values: 1 (Low), 2 (Medium), 3 (High), 4 (Urgent).
- **Priority input** → In `cli.js`, users can specify priority when creating a task (e.g., `node cli.js create "Buy milk" -p 3`).
- **Priority orchestration** → In `app.js`, the priority value from CLI is passed into the Task constructor and stored with the task object.
- **Priority persistence** → In `storage.js`, the priority field is saved alongside `id`, `title`, and `status` in the JSON file.
- **Priority usage** → In `app.js`, the `listTasks()` method supports filtering by priority via `getTasksByPriority()`. Priority filtering allows users to view only tasks of a specific priority level, but there is no global automatic priority sort in the general task list.
- **Priority updates** → Tasks can be edited to change their priority after creation, ensuring flexibility in task management.

### Misconceptions Clarified
- I initially thought priority was text-based (High, Medium, Low), but it's actually numeric (1-4).
- I assumed priority tasks were automatically sorted on display, but they're only filtered by priority if explicitly requested.
- Priority is functional — it can be set at creation, updated after, and filtered during listing.
- I assumed priority could only be set at creation, but discovered it can be updated later.
- I thought only `models.js` mattered for priority, but learned that `cli.js`, `app.js`, and `storage.js` all play roles in handling it.

# Exercise 3

# Code Understanding Journal

## Part 3: Domain Model Exploration (Prompt 3)

### Current Understanding
- The system seems to be modeling a **Task Management application**.
- I think **Task** and **User** are related because tasks are created and owned by users.
- The `priority` field appears to represent the **urgency or importance** of a task.
- I'm confused about why tasks have both a `status` and a `priority` — aren’t they overlapping concepts?

### Senior Developer Guidance
1. **Validation & Corrections**
   - Correct: the system models tasks and their lifecycle.
   - Clarification: `status` (progress) and `priority` (urgency) are distinct concepts that complement each other.
   - Tasks belong to users, even if the code doesn’t explicitly define a `User` class.

2. **Core Domain Concepts**
   - **Task** → central entity with `id`, `title`, `status`, `priority`.
   - **Status** → progress marker (done vs. not done).
   - **Priority** → urgency marker (high, medium, low).
   - **User** → implicit domain actor who creates and manages tasks.
   - **Persistence** → tasks saved in `tasks.json` to survive restarts.

3. **Relationships in Business Terms**
   - A **User** creates a **Task**.
   - Each **Task** has a **status** (progress) and a **priority** (importance).
   - Tasks are persisted in storage, reflecting business processes like “mark done” or “set priority.”
   - CLI commands are the business interface translating user actions into domain changes.

4. **Domain Terminology**
   - *Encapsulation*: wrapping task properties in a class.
   - *Persistence*: saving tasks to disk.
   - *Orchestration*: app.js coordinating logic.
   - *Domain Model*: conceptual representation of tasks, users, and attributes.

5. **Connection to User Features**
   - **Add task** → create with title + priority.
   - **Mark done** → update status to complete.
   - **List tasks** → filter by status or priority; there is no combined sort-by-priority behavior for every list.
   - **Edit task** → change priority or title.

### Questions to Test Understanding
- Why does a task need both status and priority?  
- How would the system behave if priority was missing?  
- What business rule determines when a task’s status changes?  
- How do users interact with priority through CLI commands?  
- What happens when two tasks have the same priority?

Exercise 4

Presentation Script (3–5 minutes)

1. High-Level Overview
The application is a Task Manager built with four main components:

cli.js → entry point for user commands

app.js → orchestrates logic

models.js → defines task structure

storage.js → handles persistence

➡ Together, they form a cycle: input → orchestration → data structure → persistence → feedback.

2. Key Features
Task Creation → User adds a task via CLI, app.js builds it using models.js, and storage.js saves it.

Task Prioritization → Priority is set at creation, saved in storage, and used in app.js to filter tasks.

Task Completion → User marks a task done, app.js calls storage.js to update status, and the change is persisted.

3. Interesting Design Pattern
Separation of Concerns → Each file has a distinct responsibility.

This makes the system modular, easier to maintain, and clear to understand.

Example: models.js only defines structure, while storage.js only handles persistence.

4. Challenges & How Prompts Helped
Challenge: Distinguishing between status and priority — I initially thought they overlapped.

Prompts Helped:

Prompt 1 guided me through basic feature exploration.

Prompt 2 deepened my understanding with targeted questions.

Prompt 3 connected code to domain concepts and business logic.

Prompt 4 now consolidates everything into a clear narrative.

5. Closing Reflection
My process was iterative: start with surface-level notes, then refine through guided prompts.

The journal shows my progression from “just reading code” to “understanding the business logic.”

This exercise taught me how to systematically explore, question, and document a codebase.

## Exercise: Algorithm Deconstruction Challenge

### Algorithm Chosen
Task priority sorting in JavaScript (calculateTaskScore, sortTasksByImportance, getTopPriorityTasks).

### Key Understanding
- Weighted scoring system combining priority, due date, status, tags, and recent updates.
- Produces a numeric score for each task, then sorts and slices top N.

### Worked Example (Scores)
- Fix login bug → 83
- Submit financial report → 63
- Update project roadmap → 25
- Team meeting notes → 5

Sorted output: Fix login bug, Submit financial report, Update project roadmap, Team meeting notes.

### Insights
- Multi-factor scoring, deadline sensitivity, noise reduction, extensibility.

### Reflection Summary
1. AI explanation clarified numeric contributions.
2. Difficulties: edge cases (no due date, conflicting signals, multiple tags).
3. Junior dev explanation: “Give each task points, sort by highest.”
4. Tested with worked example scores.
5. Improvements: configurable weights, recurring tasks, adaptive scoring.
