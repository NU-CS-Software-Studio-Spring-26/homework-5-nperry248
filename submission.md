# Homework 5 Submission

Repository: [homework-5-nperry248](https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-nperry248)

## Part 1 — Set up

- [.cursorignore](https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-nperry248/blob/main/.cursorignore)

## Part 2 — Teach Cursor your codebase

- [AGENTS.md](https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-nperry248/blob/main/AGENTS.md)
- [.cursor/rules/rails-conventions.mdc](https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-nperry248/blob/main/.cursor/rules/rails-conventions.mdc)
- [.cursor/rules/security.mdc](https://github.com/NU-CS-Software-Studio-Spring-26/homework-5-nperry248/blob/main/.cursor/rules/security.mdc)

## Part 3 — Various-mode prompting

### Ask mode (investigate)

**Prompt used:**

```
Where in this codebase is it decided which Todo attributes can be mass-assigned on create and update (strong parameters)? Trace the full path from the HTTP request through the controller to the database columns that are actually writable today. Cite the exact files and line numbers. Do not propose changes.
```

**Files and line numbers returned (verified against repo):**

| File | Lines | What it does |
|------|-------|----------------|
| `config/routes.rb` | 1–2 | `resources :todos` → `POST /todos` → `create`, `PATCH /todos/:id` → `update` |
| `app/views/todos/new.html.erb` | 5 | Renders `"form"` partial |
| `app/views/todos/_form.html.erb` | 1, 14–17 | `form_with(model: todo)`; only `description` field in the form |
| `app/controllers/todos_controller.rb` | 23–24 | `create` uses `Todo.new(todo_params)` |
| `app/controllers/todos_controller.rb` | 38–40 | `update` uses `@todo.update(todo_params)` |
| `app/controllers/todos_controller.rb` | 74–76 | **`todo_params`** — `params.expect(todo: [ :description ])` (decision point) |
| `app/models/todo.rb` | 1–2 | No extra attr filtering; inherits `ApplicationRecord` |
| `db/schema.rb` | 14–19 | Columns: `description`, timestamps, `due_date` |
| `db/migrate/20260519180933_add_due_date_to_todo.rb` | 1–5 | Migration that added `due_date` |
| `test/controllers/todos_controller_test.rb` | 18–21, 36–38 | Tests post/patch only `todo: { description: ... }` |
| `app/views/todos/_todo.json.jbuilder` | 1–2 | JSON exposes `id`, `description`, timestamps (not `due_date`) |

### Plan mode (design)

**Prompt used:**

```
I want to allow due_date on create and update. Right now only description is in strong params and the form, but due_date is already on the todos table.

Propose a numbered plan of changes (files to edit and tests to add). No migration needed unless you see a reason. Do not write code.
```

**Plan returned (final, after shortening):**

1. **`app/controllers/todos_controller.rb`** — Add `:due_date` to `todo_params` next to `:description`.
2. **`app/views/todos/_form.html.erb`** — Add a `due_date` input (optional field).
3. **`test/controllers/todos_controller_test.rb`** — Assert create and update save `due_date` when sent in params.

Run tests: `bin/rails test test/controllers/todos_controller_test.rb`

**My edits:** Dropped optional follow-ups (show/index display, jbuilder, fixtures) to keep scope to create/update only. Confirmed no migration needed (`due_date` column already in `db/schema.rb`). Planned one Agent commit per step (params → form → tests).

### Agent mode (execute one small slice)

*(pending)*

### Bad → good prompt rewrite

*(pending)*

## Part 4 — High-priority toggle (Turbo Streams)

*(pending)*
