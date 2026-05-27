# Agent brief

One-page context for AI assistants working on this repository. Facts below are taken from `Gemfile`, `Gemfile.lock`, `config/`, `app/`, `db/`, `test/`, and `bin/` in this repo.

## Stack

Rails **8.1.3** sample todo app (`TodoApp` in `config/application.rb`, `config.load_defaults 8.0`). Ruby **3.4.1** (`.ruby-version`). Database: **SQLite3** (`gem "sqlite3"`; files under `storage/*.sqlite3` per `config/database.yml`). Front end: **Hotwire** — `turbo-rails`, `stimulus-rails`, **importmap-rails** (`config/importmap.rb`); assets via **propshaft** (`stylesheet_link_tag :app` in the layout). JSON views use **jbuilder**. Tests: **Minitest** (`test/`, `require "rails/test_help"`); system tests use **Capybara** + **selenium-webdriver** (CI runs `test:system`). Production background jobs: **solid_queue** (`config.active_job.queue_adapter = :solid_queue` in `config/environments/production.rb`). Also present: solid_cache, solid_cable, Kamal/thruster (deploy). No RSpec, Devise, or Bootstrap in the Gemfile.

## Commands

Setup: `bin/setup` (bundle install, `bin/rails db:prepare`, log/tmp clear; starts `bin/dev` unless `--skip-server`). Run app: `bin/dev` (runs `bin/rails server`). Tests: `bin/rails test`; CI also uses `bin/rails db:test:prepare test test:system` (`.github/workflows/ci.yml`). Lint: `bin/rubocop` (uses `.rubocop.yml` / rubocop-rails-omakase). Security scan: `bin/brakeman`. Seeds: `bin/rails db:seed` (`db/seeds.rb` creates two sample todos). Other: `bin/rails`, `bin/rake`, `bin/importmap`, `bin/jobs` (Solid Queue CLI).

## Conventions

Single resource: `Todo` model, `TodosController`, `resources :todos` plus `GET /hello` → `todos#hello` (`config/routes.rb`). Controllers use `respond_to` with **`format.html`** and **`format.json`** only (see `app/controllers/todos_controller.rb`); **no `format.turbo_stream` in the codebase yet**. Strong params: `params.expect(todo: [ :description ])` in `todo_params` (only `:description` is permitted today; `due_date` exists on the table per `db/schema.rb` but is not in `todo_params`). Shared views: partials under `app/views/todos/` (`_todo.html.erb` uses `dom_id(todo)`; `_form.html.erb` uses `form_with`); JSON partials in `app/views/todos/*.jbuilder`. Index renders `<%= render todo %>` inside `#todos`. **No authentication or authorization** layer (no Devise/Pundit; `ApplicationController` only sets `allow_browser versions: :modern`). Forms show validation errors inline in the partial (no separate error partial).

## Don'ts

- **No new gems** without explicit approval (keep the existing Gemfile stack; do not add RSpec, Devise, etc.).
- **Do not hand-edit `db/schema.rb`**; change the database with reversible migrations under `db/migrate/`.
- **Do not expand scope beyond this todo app** (schema currently has only the `todos` table; do not import unrelated models or migrations).
- **Do not disable CSRF** in application code (no `skip_before_action :verify_authenticity_token`; layout includes `csrf_meta_tags`).
- **Do not add inline `<script>` in ERB**; JavaScript lives under `app/javascript/` and is loaded via importmap (`javascript_importmap_tags`).
- **Do not put production secrets or real user data in seeds**; use `db/seeds.rb` for sample todos only (as it does now).
