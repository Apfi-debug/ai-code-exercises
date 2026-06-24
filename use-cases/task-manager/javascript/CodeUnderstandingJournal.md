# CODE UNDERSTANDING JOURNAL

# EXERCISE: CODE EXPLORE CHALLENGE
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

## Corrections and Clarifications

- **No `User` class:** The codebase does not define a `User` domain object; ownership is implied by CLI usage but not implemented.
- **Priority is numeric:** `TaskPriority` values are numeric (1–4), not textual labels like "High/Medium/Low".
- **No automatic priority sorting:** Tasks are filtered by priority when requested (`getTasksByPriority()`); lists are not globally auto-sorted by priority.
- **CLI priority parsing caveat:** The CLI option `-p/--priority` may supply a string; the code usually converts it to a number (e.g., via `parseInt`) but ensure numeric conversion when comparing or filtering priorities.
- **Avoid overstating behaviors:** Do not claim background reordering, implicit user ownership, or other features not present in the implementation.

These corrections align the journal with the implementation in `models.js`, `app.js`, `storage.js`, and `cli.js`.

Exercise 4

Presentation Script (3–5 minutes)

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

## EXERCISE: ALGORITHM DECUNSTRUCTION CHALLENGE

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

## Reflection feedback
Challenging parts: Documenting pagination and filter logic clearly.

Prompt adjustments: Needed to specify “include examples and error handling” to get complete docs.

Most effective format: OpenAPI for machine readability, Markdown for developer onboarding.

Workflow use: Start with AI‑generated docs, refine manually, then publish in both Markdown and OpenAPI formats for team use.

# EXERCISE: README AND USER GUIDE DOCCUMENTATION

1. TaskCLI
A powerful command‑line task management tool designed for developers who prefer to stay in the terminal.

## Part A: Comprehensive README

2.2 Features
-Task Management: Create, update, and track tasks directly from your terminal
-Organization: Group tasks into projects and add tags for easy filtering
-Prioritization: Set priorities and deadlines to stay focused on what matters
-Notifications: Receive daily summary emails of pending tasks
-GitHub Integration: Link tasks to GitHub issues and pull requests
-Time Tracking: Track time spent on different tasks and generate reports

2.3 Installation

-Node.js 10.0 or higher
-npm or yarn

2.4 Quick Install

npm install -g task-cli
taskcli --version

2.5 Manual Installation

git clone https://github.com/yourusername/task-cli.git
cd task-cli
npm install
npm link

2.6 Configuration

On first run, TaskCLI will create a configuration file at ~/.taskcli/config.json. This file can be eddited using configuration command:

taskcli config set email your.email@example.com
taskcli config set githubToken your-github-token
Option	Description	Default
email	Email for notifications	None
githubToken	GitHub Personal Access Token	None
defaultProject	Default project for new tasks	"inbox"
reminderTime	Time for daily reminder emails	"08:00"
dateFormat	Date format for display	"YYYY-MM-DD"

2.7 Usage

Basic Commands

taskcli add "Implement new feature"
taskcli list
taskcli complete 42
taskcli show 42

2.8 Managing Projects

taskcli project add "Website Redesign"
taskcli project list
taskcli add "Design homepage" --project "Website Redesign"

2.9 Using Tags

taskcli tag 42 --add urgent frontend
taskcli list --tags urgent

2.10 Time Tracking

taskcli timer start 42
taskcli timer stop
taskcli report time --from 2023-01-01 --to 2023-01-31

2.11 Troubleshooting

-Command Not Found: Ensure your npm global bin directory is in your PATH.
-Authentication Errors: Verify your GitHub token has the correct permissions (repo scope).
-Database Errors: Reset the database with taskcli db:reset.

2.12 Contributing

-Fork the repository
-Create your feature branch (git checkout -b feature/amazing-feature)
-Commit your changes (git commit -m 'Add some amazing feature')
-Push to the branch (git push origin feature/amazing-feature)
-Open a Pull Request

2.13 License

This project is licensed under the MIT License — see the LICENSE file for details.

# Part B: Step-by-step guide

## Requirements to have before setting up GitHub Integration in TaskCLI

# Before starting, make sure you have:

-TaskCLI installed (version 2.0 or higher)

-A GitHub account

-Access to the repositories you want to integrate

# Step 1: Create a GitHub Personal Access Token

1. Log in to your GitHub account
2. Navigate to Settings > Developer settings > Personal access tokens
3. Click Generate new token
4. Name the token (e.g., “TaskCLI Integration”)
5. Select scopes:
   -repo (full control of     private repositories)
   -read:user (read user information)
6. Click Generate token

7. Copy the token immediately
GitHub only shows it once

# Step 2: Configure TaskCLI with Your Token

1. Open your terminal

2. Run:

taskcli config set githubToken your-token

3. Verify configuration:

taskcli config list

# Step 3: Link a GitHub Repository

1. Create or use an existing project:


taskcli project add "Website Frontend"

2. Link the project to a repository:

taskcli project link "Website Frontend" --repo "username/repository"

3. Confirm link:

taskcli project show "Website Frontend"

# Step 4: Link Tasks to GitHub Issues

1. Import an issue as a task:

taskcli github import --repo "username/repository" --issue 42

2. Reference an issue when creating a task:

taskcli add "Fix navigation bug" --github-issue 42

3. Link an existing task to an issue:

taskcli link 24 --github-issue 42

# Step 5: Use GitHub Integration Features

1. Sync issues:

taskcli github sync

2. Push updates to GitHub:

taskcli complete 24 --push-github

3. View GitHub details:

taskcli show 24 --github-details

# Troubleshooting
1. Authentication failed → Check token permissions or regenerate a new token
2. Repository not found → Verify repository name and access rights
3. Changes not syncing → Run taskcli github sync --force and check logs with taskcli log show

# Part C: TaskCLI (FAQ)
 # Start
Q: What is TaskCLI?  
A: TaskCLI is a command‑line task management tool for developers. It lets you create, organize, and track tasks directly in your terminal.

Q: How do I install TaskCLI?  
A: Install globally with npm:

npm install -g task-cli

Q: What are the system requirements?  
A: Node.js 10.0 or higher and npm. Works on Windows, macOS, and Linux.

Q: How do I get started after installation?  
A: Run taskcli init to set up configuration, then create your first task:

taskcli add "My first task"

# Features and Usage

Q: How do I create a new task?  
A: Use the add command:

taskcli add "Implement login page"

Q: How do I view tasks?  
A: Run:

taskcli list

Q: Can I set due dates?  
A: Yes, when creating or updating tasks:

taskcli add "Write docs" --due 2023-12-31
taskcli update 42 --due 2023-12-31

Q: What are projects and tags?  
A: Projects group related tasks, tags categorize tasks across projects. Example:

taskcli project add "Website Redesign"
taskcli add "Fix bug" --tags urgent backend

# Time Tracking

Q: How do I track time on a task?  
A: Start and stop timers:

taskcli timer start 42
taskcli timer stop

Q: Can I add time manually?  
A: Yes:

taskcli timer add 42 --minutes 30 --date 2023-01-15

Q: How do I view reports?  
A: Generate reports:

taskcli report time --from 2023-01-01 --to 2023-01-31

# GitHub Integration

Q: How do I link TaskCLI to GitHub?  
A: Configure your token:

taskcli config set githubToken your-github-token

Q: Can I import GitHub issues as tasks?
A: Yes:

taskcli github import --repo "username/repository" --issue 42

Q: Do status changes sync with GitHub?  
A: Yes, completing a linked task can close the GitHub issue:

taskcli complete 42 --push-github

# Troubleshooting

Q: TaskCLI command not found after installation  
A: Ensure your npm global bin directory is in your PATH (npm bin -g).

Q: Database errors or corruption  
A: Reset with:

taskcli db:reset

Q: Email notifications not working  
A: Verify configuration:

taskcli config list
taskcli email:test

Q: How do I uninstall TaskCLI completely?  
A:
npm uninstall -g task-cli
Then remove the data directory (~/.taskcli/ or %APPDATA%\taskcli\ on Windows).

# Advanced Features

Q: Can I create recurring tasks?  
A: Yes:

taskcli add "Weekly report" --recurring weekly --weekday friday

Q: How do I back up my data?  
A: Export and import:

taskcli export --format json --output backup.json
taskcli import backup.json

Q: Can I sync across multiple computers?  
A: Yes, enable cloud sync:

taskcli sync enable

Q: Is there an API?  
A: Yes, start the API server:

taskcli api start
Documentation available at http://localhost:3000/api-docs

# Reflection 

Documenting TaskCLI taught me that clarity is key. The hardest part was writing the step‑by‑step guide, since I had to break technical commands into simple, beginner‑friendly steps. I adjusted prompts to include requirements and troubleshooting, which made the guide more useful.

Creating the FAQ showed me the importance of anticipating user problems — not just listing features, but answering real questions like installation errors or GitHub sync issues.

Overall, I learned how structure (README, guide, FAQ) creates a complete documentation set. This approach will help me in future projects by making my tools easier to understand and use.

# Exercise: ERROR DIAGNOSIS CHALLENGE

## Error Analysis: Index Out of Bounds (JavaScript)
Error Description:  
The error message Uncaught TypeError: Cannot read properties of undefined (reading '0') means the code is trying to access a property (name or email) of something that doesn’t exist. In this case, the loop assumes there are always 5 users.

## Root Cause:  
The loop in renderUserList is hardcoded to run 5 times

for (let i = 0; i < 5; i++) {
  const user = users[i];
  const userName = user.name; // fails when user is undefined
}

When i reaches 3 or 4, users[i] is undefined because the array only has indices 0, 1, 2

## Solution:  
Change the loop to depend on the actual length of the users array

for (let i = 0; i < users.length; i++) {
  const user = users[i];
  const userName = user.name;
  const userEmail = user.email;
  // ... rest of code
}

## Learning Points:

1. Never hardcode array lengths unless you’re absolutely sure of the data size.
2. Use users.length or array methods like forEach to adapt to dynamic data.
3. Defensive programming helps: check if user exists before accessing properties.
4. When working with APIs, always assume the data may vary in size or structure.

## Reflection answers
-AI vs documentation: Documentation explains what undefined means, but AI helps connect it directly to the loop logic.

-Hard to diagnose manually: Without carefully checking the API response, you might assume 5 users exist and overlook the mismatch.

-Better error messages: Adding console logs like console.log(users.length) before the loop would make debugging easier.

-Concepts learned: It’s not just about fixing the loop — it’s about understanding how arrays, indices, and API responses interact in JavaScript.

# EXERCISE: PERFORMANCE OPTIMIZATION CHALLENGE

# Performance Analysis: Slow Database Query (Node.js + PostgreSQL)
Why it’s slow (plain terms):  
The query is pulling a lot of data with multiple nested subqueries (json_agg for items and status history). For customers with many orders, this means scanning large tables (order_items, order_status_history) repeatedly. Without proper indexes, PostgreSQL has to do full table scans, which explains the 8–10 second execution time.

Specific bottlenecks:

1. Nested subqueries: Each order triggers separate queries for items and status history.

2. Lack of indexes: Only primary keys are indexed. Filtering by customer_id and order_date isn’t optimized.

3. Large joins: order_items (~500k rows) and order_status_history (~300k rows) are scanned repeatedly.

4. JSON aggregation: While convenient, it adds overhead when applied to large datasets.

## Suggested Optimizations
1. To add indexes

-Index on orders(customer_id, order_date) to speed up filtering.
-Index on order_items(order_id) and order_status_history(order_id) to optimize subqueries.
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_status_history_order_id ON order_status_history(order_id);

2. Using JOINs instead of nested subqueries  
-Instead of running separate subqueries for each order, join tables once and aggregate in a single query. This reduces repeated scans.

3. Pagination / Limit results  
-If the UI doesn’t need all orders at once, fetch in pages (e.g., 20 orders at a time). This reduces query load and improves perceived performance.

... ORDER BY o.order_date DESC LIMIT 20 OFFSET 0;

4. Connection pooling & caching

-Use pg pool efficiently to avoid reconnect overhead.
-Cache frequent queries (e.g., last 10 orders) in Redis or memory

## Learning Points
-Indexes are critical: Without them, even well-written queries crawl on large datasets.
-Subqueries vs joins: Joins are usually faster when aggregating related data.
-Think in terms of query plans: Use EXPLAIN ANALYZE in PostgreSQL to see how the query executes.
-Pagination improves UX: Users rarely need all data at once.

## Reflection answers
1. Optimization showed how query structure directly impacts performance.
2. Improvements can reduce query time from ~10s to <1s with proper indexing and joins.
3. Learned that bottlenecks often come from repeated scans and missing indexes.
4. Future approach: always analyze query plans before scaling.
5. Tools: EXPLAIN ANALYZE, pg_stat_statements, and caching layers like Redis.

# EXERCISE: AI SOLUTION VERIFICATION CHALLENGE

## Scenario: 
-Buggy sorting function (mergeSort) with a subtle error in the merge function.
-Objective: Apply three verification strategies — Collaborative Solution Verification, Learning Through Alternative Approaches, and Developing a Critical Eye — to confirm the AI’s fix and document the process.

## Collaborative Solution Verification

AI’s suggestion: Replace j++ with i++ in the leftover loop.

Manual walkthrough: Tested with [3,1,2]. Without the fix, the loop never terminates correctly. With the fix, the merge completes and produces [1,2,3].

Peer-style review: The bug was quickly spotted as an index mismatch — incrementing the wrong variable.

## Learning Through Alternative Approaches
-Compared with a known correct merge sort implementation that uses concat for leftovers

return result.concat(left.slice(i)).concat(right.slice(j));

-This approach avoids manual leftover loops entirely, confirming the AI’s fix aligns with standard practice.
-Insight: Alternative implementations can highlight more elegant solutions and reduce the risk of subtle bugs

## Developing a Critical Eye
-Scrutinized assumptions: The bug wasn’t in the main merge logic but in the cleanup loops — a common blind spot.
-Edge case testing: Verified behavior with empty arrays, single-element arrays, and already-sorted arrays. All passed after the fix.
-Critical reflection: The AI’s fix was correct, but the alternative approach demonstrated a safer coding pattern.

## The verified solution

function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);
}

function merge(left, right) {
  let result = [];
  let i = 0;
  let j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] < right[j]) {
      result.push(left[i]);
      i++;
    } else {
      result.push(right[j]);
      j++;
    }
  }

  while (i < left.length) {
    result.push(left[i]);
    i++; // fixed
  }

  while (j < right.length) {
    result.push(right[j]);
    j++;
  }

  return result;
}

## Reflection
1. Confidence change: Initially low due to subtlety of the bug; now high after verification.
2. Most scrutiny required: The leftover loops — easy to overlook but critical for correctness.
3. Most valuable technique: Alternative approaches provided the clearest confirmation and reinforced best practices.
4. Learning point: Verification isn’t just about fixing bugs — it’s about building confidence in both the solution and the underlying concepts.

# EXERCISE: USING AI TO HELP WITH TESTING

## Part 1: Understanding What to Test (JavaScript)

1.1 Behavior Analysis — calculateTaskScore

1. Key behaviors to test:
-priority weighting (LOW vs URGENT).
-due date scoring (overdue, today, future).
-status impact (DONE, REVIEW).
-tag boosts (blocker, critical, urgent).
-recent update boost.

2. edge cases
-Task with URGENT priority vs LOW.
-Task with overdue due date.
-Task marked DONE.
-Task with multiple tags.
-Task updated yesterday vs 30 days ago.

1.2 Structured Test Plan 

# Functions Under Test
-calculateTaskScore → assigns numeric scores to tasks based on priority, due date, status, tags, and updates.
-sortTasksByImportance → sorts tasks by their calculated score.-getTopPriorityTasks → retrieves the highest‑priority tasks from the sorted list.

# Priority of Test Cases
1. High Priority (must test first)

-Correct scoring logic in calculateTaskScore (priority, due date, tags, status).
-Sorting order correctness in sortTasksByImportance.
-Retrieval accuracy in getTopPriorityTasks.

2. Medium Priority

-Edge cases (tasks with missing fields, invalid values).
-Multiple tasks with identical scores (tie‑breaking).

3. Low Priority

-Performance with large task lists.
-Integration with external data sources.

# Types of Tests Needed
1. Unit Tests

Each function individually (calculateTaskScore, sortTasksByImportance, getTopPriorityTasks).

2. Integration Tests

All three functions working together in a workflow.

3. Edge Case Tests

Missing due dates, invalid priorities, multiple tags, tasks marked DONE but still scored.

# Test Dependencies
-Mock task objects with different combinations of properties.
-A consistent scoring formula reference (so you know expected outputs)
-A small dataset for integration testing (3–5 tasks with varied attributes).

# Expected Outcomes
-calculateTaskScore: returns correct numeric score for each task.
-sortTasksByImportance: tasks sorted descending by score.
-getTopPriorityTasks: returns the top N tasks, correctly ordered.
-Edge cases handled gracefully (no crashes, predictable outputs).

## Part 2: Improving a Single Test

# Exercise 2.1: 
2.1.1 First Test

// calculateTaskScore.test.js
const { calculateTaskScore } = require('./taskManager');

test('basic functionality: HIGH priority task gets a higher score than LOW', () => {
  const lowTask = { priority: 'LOW', dueDate: null, status: 'IN_PROGRESS', tags: [], updatedAt: new Date() };
  const highTask = { priority: 'HIGH', dueDate: null, status: 'IN_PROGRESS', tags: [], updatedAt: new Date() };

  const lowScore = calculateTaskScore(lowTask);
  const highScore = calculateTaskScore(highTask);

  expect(highScore).toBeGreaterThan(lowScore);
});

2.1.2 Reflect & Improve

1. What is this test really verifying?
→ It’s checking priority weighting, but ignores due dates, tags, or status.
2. Is the test checking the behavior (expected outcome) or the implementation details (specific formula)?
→ Right now, it’s behavior-focused, which is good.
3. Could the test name be clearer?
→ Yes, rename it to:

javascript
test('HIGH priority tasks score higher than LOW priority tasks', () => { ... });

4. What if the task has no priority, or invalid values?
→ You should add tests for those later.
5. Right now, it only checks “greater than.” Could you assert exact values if you know the formula?
→ That would make the test more precise.

2.1.3 Improved Test

test('HIGH priority tasks score higher than LOW priority tasks', () => {
  const lowTask = { priority: 'LOW', dueDate: null, status: 'IN_PROGRESS', tags: [], updatedAt: new Date() };
  const highTask = { priority: 'HIGH', dueDate: null, status: 'IN_PROGRESS', tags: [], updatedAt: new Date() };

  const lowScore = calculateTaskScore(lowTask);
  const highScore = calculateTaskScore(highTask);

  expect(highScore).toBeGreaterThan(lowScore);
  expect(highScore).toBeGreaterThanOrEqual(10); // assuming HIGH adds +10
  expect(lowScore).toBeGreaterThanOrEqual(0);   // baseline score should not be negative
});

## Exercise 2.2: Learning From Examples

# Your Rough Test Idea

// pseudocode
test("task due tomorrow should score higher than task due next week", () => {
  task1 = { dueDate: tomorrow };
  task2 = { dueDate: nextWeek };
  expect(score(task1)).toBeGreaterThan(score(task2));
});

# Principles of a Good Test
-A strong test for due date calculation should:
-Verify relative scoring (closer due dates = higher score).
-Include edge cases (overdue, today, no due date).
-Use clear assertions (not just “greater than,” but expected ranges or exact values if formula is known).
-Be readable and maintainable (test names explain intent).

# comprehensive test for the due date calculation

test('tasks due sooner should score higher than tasks due later', () => {
  const today = new Date();
  const tomorrow = new Date(today);
  tomorrow.setDate(today.getDate() + 1);

  const nextWeek = new Date(today);
  nextWeek.setDate(today.getDate() + 7);

  const taskDueTomorrow = { priority: 'MEDIUM', dueDate: tomorrow, status: 'IN_PROGRESS', tags: [], updatedAt: today };
  const taskDueNextWeek = { priority: 'MEDIUM', dueDate: nextWeek, status: 'IN_PROGRESS', tags: [], updatedAt: today };

  const scoreTomorrow = calculateTaskScore(taskDueTomorrow);
  const scoreNextWeek = calculateTaskScore(taskDueNextWeek);

  // Clear assertion: tomorrow’s task should have a higher score
  expect(scoreTomorrow).toBeGreaterThan(scoreNextWeek);

  // Optional: check that both scores are positive
  expect(scoreTomorrow).toBeGreaterThanOrEqual(0);
  expect(scoreNextWeek).toBeGreaterThanOrEqual(0);
});

# Part 3: Test-Driven Development Practice

## Exercise 3.1: TDD for a New Feature

1. Writing a failing test

test('tasks assigned to current user get +12 score boost', () => {
  const today = new Date();
  const taskForUser = { 
    priority: 'MEDIUM', 
    dueDate: today, 
    status: 'IN_PROGRESS', 
    tags: [], 
    updatedAt: today, 
    assignedTo: 'currentUser' 
  };

  const taskForOther = { 
    priority: 'MEDIUM', 
    dueDate: today, 
    status: 'IN_PROGRESS', 
    tags: [], 
    updatedAt: today, 
    assignedTo: 'otherUser' 
  };

  const scoreUser = calculateTaskScore(taskForUser);
  const scoreOther = calculateTaskScore(taskForOther);

  // Expect currentUser’s task to score exactly 12 points higher
  expect(scoreUser).toBe(scoreOther + 12);
});

# minimal code to pass

if (task.assignedTo === 'currentUser') {
  score += 12;
}

# Refactor if Necessary

const USER_BOOST = 12;
if (task.assignedTo === 'currentUser') {
  score += USER_BOOST;
}

# Write the Next Test

test('tasks without assignedTo field do not get boost', () => {
  const today = new Date();
  const task = { 
    priority: 'MEDIUM', 
    dueDate: today, 
    status: 'IN_PROGRESS', 
    tags: [], 
    updatedAt: today 
    // no assignedTo
  };

  const score = calculateTaskScore(task);

  // Expect baseline score without boost
  expect(score).toBeGreaterThanOrEqual(0);
  expect(score).not.toBe(12); 
});

## Exercise 3.2: TDD for Bug Fix

1. Write a Failing Test

test('days since update should calculate correctly', () => {
  const today = new Date();
  const fiveDaysAgo = new Date(today);
  fiveDaysAgo.setDate(today.getDate() - 5);

  const task = { 
    priority: 'LOW', 
    dueDate: null, 
    status: 'IN_PROGRESS', 
    tags: [], 
    updatedAt: fiveDaysAgo 
  };

  const score = calculateTaskScore(task);

  // Expect the function to recognize exactly 5 days difference
  // (depending on your formula, e.g., boost only if <= 3 days)
  expect(score).toBe(/* expected value based on correct formula */);
});

2. Implement Minimal Code

const daysSinceUpdate = Math.floor(
  (Date.now() - task.updatedAt.getTime()) / (1000 * 60 * 60 * 24)
);

3. Refactor if Necessary 

function getDaysSinceUpdate(updatedAt) {
  return Math.floor((Date.now() - updatedAt.getTime()) / (1000 * 60 * 60 * 24));
}

4. Write Next Tests

test('task updated today should have 0 days since update', () => {
  const today = new Date();
  const task = { updatedAt: today };
  expect(getDaysSinceUpdate(task.updatedAt)).toBe(0);
});

test('task updated exactly 3 days ago should return 3', () => {
  const today = new Date();
  const threeDaysAgo = new Date(today);
  threeDaysAgo.setDate(today.getDate() - 3);
  const task = { updatedAt: threeDaysAgo };
  expect(getDaysSinceUpdate(task.updatedAt)).toBe(3);
});

test('task updated long ago should return large number', () => {
  const today = new Date();
  const thirtyDaysAgo = new Date(today);
  thirtyDaysAgo.setDate(today.getDate() - 30);
  const task = { updatedAt: thirtyDaysAgo };
  expect(getDaysSinceUpdate(task.updatedAt)).toBe(30);
});

# Part 4: Integration Testing

## Exercise 4.1: Testing the Full Workflow

1. Identify Scenarios

Integration tests verifies:

-Tasks are scored correctly by calculateTaskScore.
-asks are sorted properly by sortTasksByImportance.
-The top tasks are retrieved accurately by getTopPriorityTasks.

Scenarios to test:

-Mixed priorities and due dates.
-Tasks with identical scores (tie‑breaking).
-Edge cases (no tasks, all tasks DONE).

2. Design Test Data

const tasks = [
  { priority: 'LOW', dueDate: null, status: 'IN_PROGRESS', tags: [], updatedAt: new Date(), assignedTo: 'otherUser' },
  { priority: 'URGENT', dueDate: new Date(), status: 'IN_PROGRESS', tags: ['critical'], updatedAt: new Date(), assignedTo: 'currentUser' },
  { priority: 'MEDIUM', dueDate: new Date(Date.now() + 86400000), status: 'IN_PROGRESS', tags: [], updatedAt: new Date(), assignedTo: 'otherUser' },
  { priority: 'HIGH', dueDate: new Date(Date.now() + 604800000), status: 'IN_PROGRESS', tags: [], updatedAt: new Date(), assignedTo: 'otherUser' }
];

3. Write Integration Test

test('integration: workflow scores, sorts, and retrieves top tasks', () => {
  const scoredTasks = tasks.map(task => ({
    ...task,
    score: calculateTaskScore(task)
  }));

  const sortedTasks = sortTasksByImportance(scoredTasks);
  const topTasks = getTopPriorityTasks(sortedTasks, 2);

  // Assertions:
  // 1. URGENT + critical + currentUser should be top
  expect(topTasks[0].priority).toBe('URGENT');
  expect(topTasks[0].tags).toContain('critical');
  expect(topTasks[0].assignedTo).toBe('currentUser');

  // 2. Next should be HIGH or MEDIUM depending on due date scoring
  expect(topTasks.length).toBe(2);
  expect(topTasks[1].priority === 'HIGH' || topTasks[1].priority === 'MEDIUM').toBeTruthy();

  // 3. Sorted list should be descending by score
  for (let i = 0; i < sortedTasks.length - 1; i++) {
    expect(sortedTasks[i].score).toBeGreaterThanOrEqual(sortedTasks[i + 1].score);
  }
});

4. Reflection
-This test verifies the entire workflow.
-It ensures that scoring, sorting, and retrieval are consistent.
-expand with edge cases (empty list, all DONE tasks, multiple identical scores).

## Discussion Document: Using AI to Help with Testing

# Part 1: Test Plan Summary
1. Functions under test:
-calculateTaskScore
-sortTasksByImportance
-getTopPriorityTasks
2. Priority of test cases:
-Core scoring logic (priority, due date, tags, status).
-Sorting correctness.
-Retrieval accuracy.
-Edge cases (missing fields, invalid values).
-Performance with large lists.
3. ypes of tests: Unit, integration, edge case.
4. Dependencies: Mock task objects, scoring formula reference, small datasets.
5. Expected outcomes: Correct scores, sorted order, accurate retrieval, graceful handling of edge cases.

# Part 2: Improved Unit Tests
1. Basic test: HIGH priority > LOW priority.
2. Improved test: Clearer naming, precise assertions, edge case coverage.
3. Due date test: Tasks due sooner score higher than later tasks.
4. Edge cases: Overdue tasks, no due date, tasks due today, identical due dates.

# Part 3: TDD Practice
1. New Feature (+12 boost for current user)
-Failing test: Verified that tasks assigned to currentUser should score 12 points higher.
-Minimal code: Added if (task.assignedTo === 'currentUser') score += 12;.
-Refactor: Extracted constant USER_BOOST = 12.
-Next test: Tasks without assignedTo field do not get boost.

2. Bug Fix (days since update)
-Failing test: Exposed incorrect calculation for days since update.
-Minimal fix: Used (Date.now() - updatedAt.getTime()) / (1000*60*60*24).
-Refactor: Extracted helper getDaysSinceUpdate().
-Regression tests: Verified 0 days (today), 3 days ago, 30 days ago.

# Part 4: Integration Test
1. Verified workflow:

-calculateTaskScore assigns correct scores.
-sortTasksByImportance orders tasks descending.
-getTopPriorityTasks retrieves top N tasks.

2. Assertions:

-URGENT + critical + currentUser task is top.
-Sorted list is descending by score.
-Edge cases handled (empty list, all DONE tasks).

# Part 5: Reflection
1. Through this exercise, I learned:
2. Behavior vs implementation: Good tests focus on expected outcomes, not internal formulas.
3. Edge cases matter: Overdue tasks, missing fields, and identical scores reveal weaknesses.
4. TDD discipline: Writing failing tests first forces clarity about expected behavior.
5. Integration perspective: Testing the workflow ensures functions don’t just work individually but also together.
6. AI as a partner: Using prompts to reflect on behaviors and edge cases helped me think more critically about testing, rather than just generating code.

# EXERCISE: UNDERSTANDING WHAT TO CHANGE WITH AI

## Exercise 1: Code Readability Improvement (Java)

1. Identified Issues
-Class names: UserMgr → should be UserManager for clarity.
-Variable names: u_list → users; db → databaseConnection.
-Method names: a → addUser; f → findUserByUsername.
-Class U → should be User.
-SQL injection risk: concatenating strings directly into SQL.

2. Suggested improvements by AI

class UserManager {
    private List<User> users;
    private DBConn databaseConnection;

    public UserManager(DBConn databaseConnection) {
        this.databaseConnection = databaseConnection;
        this.users = new ArrayList<>();
    }

    public boolean addUser(String username, String password, String email) {
        if (username.length() < 3 || password.length() < 8 || !email.contains("@")) {
            return false;
        }

        for (User user : users) {
            if (user.getUsername().equals(username)) {
                return false;
            }
        }

        User newUser = new User(username, password, email);
        users.add(newUser);
        return databaseConnection.execute(
            "INSERT INTO users VALUES ('" + username + "', '" + password + "', '" + email + "')"
        );
    }

    public User findUserByUsername(String username) {
        for (User user : users) {
            if (user.getUsername().equals(username)) {
                return user;
            }
        }
        return null;
    }
}

class User {
    private String username;
    private String password;
    private String email;

    public User(String username, String password, String email) {
        this.username = username;
        this.password = password;
        this.email = email;
    }

    public String getUsername() { return username; }
    public String getPassword() { return password; }
    public String getEmail() { return email; }
}

## Exercise 2: Function Refactoring 
1. Identified Issues
-The function is too long and mixes validation, pricing, inventory updates, and reporting.
-Hard to test individual parts.

2. AI-Suggested Refactoring
-validate_order(order, inventory, customer_data)
-calculate_price(order, inventory, customer_data)
-calculate_shipping(price, customer_data)
-apply_tax(price)
-update_inventory(item_id, quantity, inventory)

## Exercise 3: Code Duplication Detection (JavaScript)
1. Identified Issues
-Repeated loops for average and highest calculations.
-Same logic applied to age, income, and score.

2. AI-Suggested Consolidation

function calculateStats(userData, key) {
  let total = 0;
  let highest = userData[0][key];

  for (let i = 0; i < userData.length; i++) {
    total += userData[i][key];
    if (userData[i][key] > highest) {
      highest = userData[i][key];
    }
  }

  return {
    average: total / userData.length,
    highest: highest
  };
}

function calculateUserStatistics(userData) {
  return {
    age: calculateStats(userData, 'age'),
    income: calculateStats(userData, 'income'),
    score: calculateStats(userData, 'score')
  };
}

## Reflection
-Most useful strategy: Function Refactoring — breaking down large functions into smaller ones makes testing and debugging far easier.
-AI surprises: It often catches naming conventions and duplication patterns that humans overlook.
-Disagreements: Sometimes AI suggests over-engineering (too many helper functions). Balance readability with simplicity.
-Adaptation: Tailor prompts to your stack — e.g., emphasize Pythonic idioms or JavaScript ES6 features.
-Safeguards: Always run tests before refactoring production code. AI suggestions should be reviewed by humans.

# EXERCISE: FUNCTION DECOMPOSITION CHALLENGE.

## JavaScript validation function

1. Distinct Responsibilities
-The original validateUserData function is handling too many jobs:
-Required field checks (registration vs profile update)
-Username validation (length, characters, uniqueness)
-Password validation (length, complexity, confirmation)
-Email validation (format, uniqueness)
-Date of birth validation (valid date, age range)
-Address validation (required fields, postal code formats)
-Phone validation (basic regex)
-Custom validations (user-defined rules)

2. Decomposition Plan
-Clear names and single responsibilities

function validateRequiredFields(userData, requiredFields) { ... }
function validateUsername(username, options) { ... }
function validatePassword(password, confirmPassword) { ... }
function validateEmail(email, options) { ... }
function validateDateOfBirth(dob) { ... }
function validateAddress(address) { ... }
function validatePhone(phone) { ... }
function applyCustomValidations(userData, customValidations) { ... }

3. Refactored Main Function

function validateUserData(userData, options = {}) {
  const errors = [];
  const isRegistration = options.isRegistration || false;

  if (isRegistration) {
    errors.push(...validateRequiredFields(userData, ['username', 'email', 'password', 'confirmPassword']));
    errors.push(...validateUsername(userData.username, options));
    errors.push(...validatePassword(userData.password, userData.confirmPassword));
  } else {
    errors.push(...validateRequiredFields(userData, ['firstName', 'lastName', 'dateOfBirth', 'address']));
  }

  errors.push(...validateEmail(userData.email, options));
  errors.push(...validateDateOfBirth(userData.dateOfBirth));
  errors.push(...validateAddress(userData.address));
  errors.push(...validatePhone(userData.phone));
  errors.push(...applyCustomValidations(userData, options.customValidations));

  return errors.filter(Boolean);
}

4. Documentation and Benefits
Readability: Each helper name explains its purpose.

Maintainability: Easier to update rules (e.g., password complexity) without touching the main function.

Reusability: Functions like validateEmail or validatePhone can be reused in other modules.

Testability: Each helper can be unit-tested independently.

# Reflector

Breaking down the JavaScript validation function made the code far easier to read. The hardest part was untangling nested conditionals, especially in password and address validation. The most reusable helper is validateEmail, since email checks are common across projects. I’d adapt these prompts to my own stack by emphasizing ES6 features like arrow functions and array methods. Before applying AI-suggested refactoring in production, I’d safeguard with unit tests and peer reviews

# EXERCISE: DESIGN PATTERN IMPLEMETATION CHALLENGE

## Reflection answers
1. Maintainability: The pattern reduced duplication, organized logic into reusable components, and made the code easier to read and extend.
2. Future changes: Adding new features or integrations will be simpler and less risky since they can be introduced without rewriting the whole system.
3. Unexpected challenges: Restructuring the original code while preserving behavior was tricky, and it required careful testing and choosing the right level of abstraction.

# EXERCISE: APPLYING AI TO DEEPEN PROGRAMMING LANGUAGE UNDERSTANDING

# Activity 1: Idiomatic Code Transformation
Learning 1: Writing idiomatic code improves readability and makes it easier for others to understand.

Learning 2: Using built‑in language features reduces boilerplate and prevents reinventing the wheel.

Learning 3: Seeing side‑by‑side comparisons helped me recognize best practices I wasn’t applying before.

# Activity 2: Code Quality Detective
Learning 1: Reviewing older code highlighted common mistakes like poor naming and duplicated logic.

Learning 2: Creating a checklist gave me a practical tool for future reviews.

Learning 3: Understanding why improvements matter (performance, readability, maintainability) made me more intentional in my coding.

# Activity 3: Understanding Language Feature
Learning 1: Exploring advanced features (like decorators or generators) expanded my toolkit beyond basics.

Learning 2: Practical use cases showed me how these features solve real problems.

Learning 3: Implementing a small project helped me avoid common mistakes and reinforced the concept.

# Discussion Themes
Common themes included: the importance of readability, the value of refactoring, and how AI prompts accelerate learning.

Sharing code changes helped me to see different approaches to the same problem.

# EXERCISE: LEARNING A NEW PROGRAMMING LANGUAGE WITH AI

## Reflection answers

1. Effective strategies: The four‑step prompting strategy was the most effective because it gave me a clear path from understanding concepts to implementing and verifying them. It kept my learning structured and prevented me from rushing into code without context.

2. Surprises: I was surprised by how different the target language’s philosophy was compared to my source language, especially in how it handles features I assumed worked the same way. This made me realize the importance of adjusting mental models early.

3. Mental models: My source language habits helped me grasp syntax quickly, but sometimes they caused misconceptions when I expected similar behavior. Recognizing these differences was key to avoiding mistakes.

4. Next time: Next time, I would spend more time exploring conceptual differences before coding. This would help me avoid confusion and make my practice sessions more efficient.

5. Remaining gaps: I still need to deepen my understanding of advanced features and idiomatic best practices. Building more projects will help me close these gaps and gain confidence in real‑world use.

# EXERCISE: UNDERSTANDING FASTAPI CODE PARTTERNS

## Reflection answers

1. Architecture understanding: Implementing the logging feature helped me see how the repository, service, and dependency injection layers fit together. It showed me how new functionality can be added without breaking the existing flow.

2. Useful design patterns: The repository pattern and dependency injection were the most useful because they separated concerns and made the code easier to extend. Middleware also stood out as a clean way to add cross‑cutting features like timing or logging.

3. Explaining repository & DI: I would explain the repository pattern as a way to centralize database operations, and dependency injection as a way to provide resources (like sessions or services) automatically without hard‑coding them. Together, they make the code more modular and testable.

4. Tracing execution flow: Tracing the request flow for /admin/users/ helped me understand where authentication, authorization, and middleware fit in. It clarified exactly where new logic like logging should be added.

