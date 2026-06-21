<<<<<<< HEAD
# CODE UNDERSTANDING JOURNAL

# EXERCISE: CODE EXPLORE CHALLENGE
=======
# Code Understanding Journal

>>>>>>> b11c6784a8b4500514819f93b6bf3cce65b9fe78
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

<<<<<<< HEAD
## Corrections and Clarifications

- **No `User` class:** The codebase does not define a `User` domain object; ownership is implied by CLI usage but not implemented.
- **Priority is numeric:** `TaskPriority` values are numeric (1–4), not textual labels like "High/Medium/Low".
- **No automatic priority sorting:** Tasks are filtered by priority when requested (`getTasksByPriority()`); lists are not globally auto-sorted by priority.
- **CLI priority parsing caveat:** The CLI option `-p/--priority` may supply a string; the code usually converts it to a number (e.g., via `parseInt`) but ensure numeric conversion when comparing or filtering priorities.
- **Avoid overstating behaviors:** Do not claim background reordering, implicit user ownership, or other features not present in the implementation.

These corrections align the journal with the implementation in `models.js`, `app.js`, `storage.js`, and `cli.js`.

=======
>>>>>>> b11c6784a8b4500514819f93b6bf3cce65b9fe78
Exercise 4

Presentation Script (3–5 minutes)

<<<<<<< HEAD
1. High-level overview (20s)
The project is a small CLI Task Manager with four modules: `cli.js` (input), `app.js` (orchestration), `models.js` (Task model), and `storage.js` (JSON persistence). Flow: input → orchestrator → model → storage → output.

2. Core features (60s)
- Create tasks (title, numeric priority 1–4, due date, tags).
- List and filter by status, priority, or overdue; update status/priority/due date; add/remove tags; delete; view stats.
Note: priority is used for filtering (not global auto-sorting) and is persisted to `tasks.json`.

3. Design & implementation highlights (40s)
- Separation of concerns and encapsulation: `Task` methods (e.g., `update`, `markAsDone`) encapsulate behavior; `TaskManager` delegates persistence to `TaskStorage`.
- Storage rehydrates date fields on load; CLI parses options and converts types (ensure numeric conversion for priority).

4. Key takeaways and suggestions (40s)
- Strengths: modular structure, clear responsibilities, easy to test and extend.
- Improvements for full credit: add explicit `User` ownership model if required by the spec, add CLI validation for numeric priority, and optionally add a sort-by-priority view.

5. Closing (20s)
Summarize: clear architecture, correct handling of priority and status, and a few small improvements that would increase robustness and feature completeness. Invite questions.
=======
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
>>>>>>> b11c6784a8b4500514819f93b6bf3cce65b9fe78

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
<<<<<<< HEAD

# EXERCISE: KNOWING WHERE TO START

# Exercise Part 1: Understanding Project Structure

I looked at the repo layout: app.js, cli.js, models.js, storage.js.

package.json confirms Node.js project, dependencies include commander.

My first guess was that it used a database, but actually persistence is JSON file storage.

Entry points: app.js (main logic), cli.js (command-line interface).

Architectural style: layered (CLI → app → storage).

Key components:

Task model → defines attributes.

Storage → handles saving/loading tasks.

CLI → parses commands.

App → coordinates everything.

Reflection: Initially thought DB-backed, but corrected after checking storage.js. Clear separation of responsibilities makes it easier to navigate.

# Exercise Part 2: Finding Feature Implementation

## Initial Search
Looked through the repo for anything related to export or file operations.

No existing export functionality found.

Found storage.js → handles saving/loading tasks to JSON file.

Saw some data transformation logic when tasks are serialized/deserialized.

No direct CSV utilities, but storage.js could be extended.

## Hypothesis

CSV export functionality would likely belong in storage.js, since that’s where file operations already happen.

Might need to add a new utility function like exportTasksToCSV().

app.js would need a new command handler to trigger the export.

cli.js would need a new CLI command (e.g., export) to call the app logic.

## Applying the prompt

Used the Finding Feature Implementation Locations prompt with AI:

Confirmed that file operations are centralized in storage.js.

Suggested reusing existing serialization logic but formatting output as CSV instead of JSON.

Pointed out that CLI commands are parsed in cli.js, so a new command would be required.

## Findings

Implementation location:

Add new function in storage.js → exportTasksToCSV().

Update app.js → add handler for export command.

Update cli.js → add new CLI command export.

Related components affected:

models.js → may need to ensure task fields are consistently structured for CSV.

storage.js → main place for file writing.

cli.js → user-facing command.

app.js → orchestrates the flow.

## Plan for implimentation

In storage.js, write a function to transform tasks into CSV format and save to tasks.csv.

In app.js, add a new function handleExport() that calls storage.exportTasksToCSV().

In cli.js, add a new CLI command export that triggers app.handleExport().

Test by running CLI → node cli.js export → check if tasks.csv is created with correct data.

# Exercise part 3: Understanding Domain Level

## Extract domaim level

Core entities spotted:

Task → main unit of work.

TaskStatus → tracks progress (done vs. not done).

TaskPriority → tracks urgency (high, medium, low).

Business logic:

Tasks can be created, updated, marked complete.

Priority and status are independent (a task can be incomplete but high priority).

Terminology:

“Persistence” → saving tasks to tasks.json.

“CLI commands” → business interface for users.

## Initial understanding

Diagram
User (via CLI)
   |
   v
Task (id, title, description, status, priority)
   |
   v
Storage (tasks.json)

Entity explanations:

Task → Represents a single to‑do item with attributes.

TaskStatus → Boolean or enum showing progress.

TaskPriority → Categorical value showing urgency.

User → Implicit actor who creates/manages tasks.

Questions/confusion:

Why are status and priority separate instead of merged?

How does the system handle tasks with the same priority?

Is there any validation on task fields (e.g., empty title)?

## Applying the prompt

Used the Domain Models and Business Concepts Prompt:

Confirmed that status = progress, priority = urgency.

Clarified that tasks are persisted in JSON, not a DB.

Highlighted that CLI commands are the business interface.

Asked me test questions to check understanding.

## My questions and answers

Why does a task need both status and priority?  
→ Because status tracks completion, while priority tracks urgency. They serve different business purposes.

What happens if priority is missing?  
→ The system would still track completion, but urgency sorting/filtering would break.

What business rule determines when status changes?  
→ Status changes when the user marks a task as complete via CLI.

## Revised diagram
User (CLI)
   |
   v
App (orchestration)
   |
   v
Task (id, title, description, status, priority)
   |
   v
Storage (tasks.json)

## Glossary of domain terms used

Task → Core entity representing work.

Status → Progress indicator (done/not done).

Priority → Urgency indicator (high/medium/low).

Persistence → Saving tasks to JSON file.

CLI command → User action mapped to business logic.

Orchestration → Coordination of CLI, models, and storage in app.js.

# Exercise part 4: Practical Application

## Planning

Files to modify:

models.js → Extend Task entity to include dueDate and possibly abandoned flag.

app.js → Add logic to check overdue tasks and update status.

storage.js → Ensure persistence of new abandoned state.

cli.js → Add CLI command or option to trigger overdue check (e.g., checkOverdue).

Changes to implement:

Add dueDate property to Task in models.js.

Add abandoned status or extend TaskStatus to include “abandoned.”

In app.js, write a function markOverdueTasks() that:

Iterates through tasks.

Compares dueDate with current date.

Marks tasks as abandoned if overdue > 7 days and not high priority.

Update storage.js to persist the new status.

Update cli.js to allow user to run overdue check manually, or trigger automatically on startup.

Questions for team:

Should “abandoned” be a new status in TaskStatus, or a separate flag?

How do we define “overdue” — is it based on dueDate or creation date?

Should the check run automatically every time the app starts, or only when user runs a command?

Do we notify the user when tasks are marked abandoned?

## Reflection
The AI prompts helped me pinpoint where business rules live (app logic) and where persistence changes are needed (storage).

I’m still unsure about how dates are currently handled in the codebase — I didn’t see a dueDate field yet, so this would be a new addition.

Next steps:

Explore how tasks are currently created in models.js to see if adding dueDate fits naturally.

Check if any date libraries (like moment.js) are already used in package.json.

Draft pseudo‑code for markOverdueTasks() and test with sample tasks.

# Part 5: Discussion

## Overall Journey
Part 1 (Project Structure) → I Learned how the repo is organized, identified entry points (app.js, cli.js), and clarified misconceptions about persistence (JSON file, not DB).

Part 2 (Feature Implementation) → Practiced tracing features, realized file operations live in storage.js, and planned how CSV export could be added cleanly.

Part 3 (Domain Model) → Mapped out entities (Task, TaskStatus, TaskPriority), understood their relationships, and built a glossary of domain terms.

Part 4 (Practical Application) → Applied knowledge to a new business rule (overdue → abandoned unless high priority), identified files to modify, and outlined implementation steps

## How AI Prompts Helped
The Project Structure Prompt guided me to focus on entry points and architectural patterns.

The Feature Location Prompt helped me narrow down where file operations belong.

The Domain Model Prompt clarified terminology and tested my understanding with questions.

Overall, prompts acted like a “senior developer” walking me through the repo, correcting misconceptions, and pointing out best practices.

## Remaining Uncertainties
How dates are currently handled in tasks (no dueDate field yet).

Whether “abandoned” should be a new status or a separate flag.

How errors are managed (e.g., invalid input, failed persistence).

If the app has any hidden dependencies or libraries not yet explored.

## Next Steps
Deep dive into models.js → confirm how tasks are created and extended.

Check package.json → look for date/time libraries.

Experiment with runtime behavior → create tasks, simulate overdue cases, test persistence.

Diagram refinement → update entity relationships as new fields are added.

Team questions → clarify business rules before coding.

## Final Reflection
This exercise taught me a repeatable process for approaching unfamiliar projects:

Map the structure.

Trace features.

Understand the domain model.

Apply knowledge to real scenarios.

# EXERCISE: CODE DOCUMENTATION

# Overview
Chosen algorithm: calculateTaskPriorityScore (JavaScript version).

# Original code chosen
function calculateTaskPriorityScore(task) {
  let score = 0;

  if (task.priority === 'High') score += 3;
  else if (task.priority === 'Medium') score += 2;
  else score += 1;

  if (task.status === 'Completed') score -= 2;

  const now = new Date();
  const dueDate = new Date(task.dueDate);
  const daysUntilDue = (dueDate - now) / (1000 * 60 * 60 * 24);

  if (daysUntilDue < 0) score -= 1; // overdue
  else if (daysUntilDue <= 3) score += 2; // urgent

  return score;
}

# Document generated in prompt 1
/**
 * Calculates a numerical priority score for a given task.
 *
 * @param {Object} task - The task object to evaluate.
 * @param {string} task.priority - The priority level ("High", "Medium", "Low").
 * @param {string} task.status - The current status ("Completed", "Pending", etc.).
 * @param {string|Date} task.dueDate - The due date of the task.
 *
 * @returns {number} A score representing the task's urgency and importance.
 *
 * @throws {Error} If dueDate is invalid or cannot be parsed.
 *
 * @example
 * const task = { priority: 'High', status: 'Pending', dueDate: '2026-06-25' };
 * const score = calculateTaskPriorityScore(task);
 * // score = 5
 *
 * Notes:
 * - High priority tasks start with a higher base score.
 * - Completed tasks are penalized.
 * - Tasks overdue lose points, urgent tasks (due within 3 days) gain points.
 */

 # The insights/improvements I identified using Prompt 2
Intent and Logic Explanation

High-level intent: Assigns a numeric score to tasks so they can be sorted and filtered by urgency and importance.

Step-by-step logic:

Start with base score depending on priority.

Subtract points if task is completed.

Calculate days until due date.

Penalize overdue tasks, reward urgent tasks.

Return final score.

Assumptions:

priority is always one of High/Medium/Low.

dueDate is a valid date string.

Inline comments suggestion: Add comments before each scoring rule for clarity.

Potential improvements:

Validate dueDate before using.

Use enums/constants for priority and status values.

Allow configurable scoring weights.

# final combined documentation version
/**
 * Calculates a weighted priority score for a task based on
 * its priority level, completion status, and proximity to due date.
 *
 * Scoring rules:
 * - High priority = +3, Medium = +2, Low = +1
 * - Completed tasks = -2
 * - Overdue tasks = -1
 * - Tasks due within 3 days = +2
 *
 * @param {Object} task - Task object to evaluate.
 * @param {string} task.priority - Priority level ("High", "Medium", "Low").
 * @param {string} task.status - Current status ("Completed", "Pending", etc.).
 * @param {string|Date} task.dueDate - Due date of the task.
 *
 * @returns {number} Priority score (integer).
 *
 * @example
 * const task = { priority: 'High', status: 'Pending', dueDate: '2026-06-25' };
 * calculateTaskPriorityScore(task); // returns 5
 *
 * Edge Cases:
 * - Invalid dueDate may cause NaN results.
 * - Completed tasks can still gain urgency points if overdue.
 *
 * Suggested Improvements:
 * - Validate dueDate input.
 * - Replace string literals with enums/constants.
 * - Make scoring weights configurable.
 */

 # Reflection
Challenging parts for AI: Handling assumptions (e.g., valid dueDate) and edge cases.

Extra info needed in prompts: Clarified expected parameter types and scoring rules.

How I’d use this approach: Great for onboarding new developers — AI helps bootstrap documentation, then I refine it for accuracy and clarity.

# API DOCUMENTATION EXERCISE
Selected API Endpoint: Exercise: JavaScript/Express Product API

## Endpoint Documentation Generation
# List Products API

**Purpose**: Retrieve a list of products with optional filtering, sorting, and pagination.

**Endpoint**: `GET /api/products`

**Query Parameters**:
- `category` (string, optional): Filter by category.
- `minPrice` (number, optional): Minimum price filter.
- `maxPrice` (number, optional): Maximum price filter.
- `sort` (string, optional, default: `createdAt`): Field to sort by.
- `order` (string, optional, default: `desc`): Sort order (`asc` or `desc`).
- `page` (number, optional, default: 1): Page number.
- `limit` (number, optional, default: 20, max: 100): Items per page.
- `inStock` (boolean, optional): When true, only products with stock > 0.

**Response**:
- `200 OK`: JSON object with `products` array and `pagination` metadata.
- `500 Internal Server Error`: JSON error object.

**Authentication**: None required.

**Error Responses**:
- `500`: Server error, failed to fetch products.

**Examples**:
1. `GET /api/products?category=electronics`  
   → Returns products in electronics category.
2. `GET /api/products?minPrice=20&maxPrice=100&sort=price&order=asc&page=1&limit=10`  
   → Returns products priced between $20–$100, sorted by price ascending.

**Notes**:
- Uses MongoDB for storage.
- IDs are ObjectId strings.
- Timestamps are ISO 8601 UTC.

## Prompt 2 to convert the documentation to a different format 

openapi: 3.0.0
info:
  title: Products API
  version: 1.0.0
servers:
  - url: http://localhost:3000
    description: Development server
paths:
  /api/products:
    get:
      summary: List products
      description: Retrieve products with optional filtering, sorting, and pagination
      parameters:
        - in: query
          name: category
          schema: { type: string }
          description: Filter by product category
        - in: query
          name: minPrice
          schema: { type: number, format: float }
          description: Minimum price filter
        - in: query
          name: maxPrice
          schema: { type: number, format: float }
          description: Maximum price filter
        - in: query
          name: sort
          schema: { type: string, default: createdAt, enum: [createdAt, price, name] }
          description: Field to sort by
        - in: query
          name: order
          schema: { type: string, enum: [asc, desc], default: desc }
          description: Sort order
        - in: query
          name: page
          schema: { type: integer, default: 1, minimum: 1 }
          description: Page number
        - in: query
          name: limit
          schema: { type: integer, default: 20, minimum: 1, maximum: 100 }
          description: Items per page
        - in: query
          name: inStock
          schema: { type: boolean }
          description: Filter only products with stock > 0
      responses:
        '200':
          description: Products retrieved successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  products:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
                  pagination:
                    $ref: '#/components/schemas/Pagination'
        '400':
          description: Invalid query parameters
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '500':
          description: Server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
      security: []

components:
  schemas:
    Product:
      type: object
      required: [id, name, price]
      properties:
        id:
          type: string
          description: MongoDB ObjectId
          example: "507f1f77bcf86cd799439011"
        name:
          type: string
          description: Product name
          example: "Wireless Mouse"
        category:
          type: string
          description: Product category
          example: "electronics"
        price:
          type: number
          format: float
          description: Product price in USD
          example: 29.99
        stock:
          type: integer
          description: Available quantity
          example: 50
        createdAt:
          type: string
          format: date-time
          description: ISO 8601 creation timestamp
          example: "2026-06-20T10:30:00Z"
        updatedAt:
          type: string
          format: date-time
          description: ISO 8601 last update timestamp
          example: "2026-06-20T10:30:00Z"
    
    Pagination:
      type: object
      properties:
        page:
          type: integer
          example: 1
        limit:
          type: integer
          example: 20
        total:
          type: integer
          description: Total number of products
          example: 100
        pages:
          type: integer
          description: Total number of pages
          example: 5
    
    Error:
      type: object
      required: [error]
      properties:
        error:
          type: string
          description: Error message
          example: "Invalid query parameters"
        status:
          type: integer
          example: 500
        timestamp:
          type: string
          format: date-time
          example: "2026-06-20T10:30:00Z"

## API Usage Guide Creation

Audience: Junior developers integrating with the API.
Tone: Friendly and practical.

Guide:

Authentication: No authentication required.

Formatting requests: Use query parameters for filtering, sorting, pagination.

Handling responses: Success returns products array + pagination.

Common errors:

500 Server error → retry or check server logs.

Example code (JavaScript):
fetch('https://api.example.com/api/products?category=electronics&limit=10')
  .then(res => res.json())
  .then(data => console.log(data.products));

## Reflection or Documention feedback
Challenging parts: Documenting pagination and filter logic clearly.

Prompt adjustments: Needed to specify “include examples and error handling” to get complete docs.

Most effective format: OpenAPI for machine readability, Markdown for developer onboarding.

Workflow use: Start with AI‑generated docs, refine manually, then publish in both Markdown and OpenAPI formats for team use.

# Exercise: README and User Guide Documentation

## Prompt 1
Comprehensive README Generation

Draft README sections:

Overview → Short description of Task Manager system.

Installation → Clone repo, run npm install, start app.

Usage → CLI commands (add, list, delete, export).

Features → Task creation, prioritization, overdue/abandoned rule, CSV export.

Contributing → Guidelines for developers.

License → Basic license info.

## Step-by-step Guide Creation

Feature chosen: Export tasks to CSV.
Guide includes:

Run CLI command node cli.js export.

App calls app.js → handleExport().

storage.js writes tasks to tasks.csv.

Output file appears in project root.

Example:
"node cli.js export"
-Creates tasks.csv with task data.

## FAQ Document Creation prompt 3

Sample FAQs:

Q: How do I install the Task Manager?  
A: Clone repo, run npm install, then node cli.js.

Q: Where are tasks stored?  
A: In tasks.json file.

Q: How do I mark a task as abandoned?  
A: The system automatically marks overdue tasks (>7 days) as abandoned unless high priority.

Q: Can I export tasks?  
A: Yes, run node cli.js export to generate tasks.csv.

Q: What happens if dueDate is missing?  
A: Task is treated as non‑urgent, no overdue logic applied.

## Reflection
Challenging parts: Deciding how much detail belongs in README vs. User Guide.

Prompt adjustments: Needed to specify “include examples and edge cases” to get complete docs.

Effective format: README for developers, step‑by‑step guide for features, FAQ for quick answers.

Workflow use: Start with AI‑generated drafts, refine manually, then publish in repo root (README.md) and docs folder (USER_GUIDE.md).
=======
>>>>>>> b11c6784a8b4500514819f93b6bf3cce65b9fe78
