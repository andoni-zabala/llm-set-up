---
name: rails-8
description: >
  Rails 8 API-only patterns including Solid Queue, Solid Cache, Solid Cable.
  Trigger: When working with Rails 8 features, API-only configuration, or Solid infrastructure.
license: MIT
metadata:
  author: ""
  version: "1.0"
  scope: [root, app, config]
  auto_invoke: "Working with Rails 8 features"
---

## Rails 8 API-Only Mode

API-only mode (`config.api_only = true`) means:

- No views, layouts, or asset pipeline
- `ApplicationController` inherits from `ActionController::API`
- JSON responses only
- No CSRF protection (API tokens instead)

---

## Solid Queue (Background Jobs)

Rails 8 replaces Redis-backed job systems with database-backed Solid Queue.

```ruby
# app/jobs/process_report_job.rb
class ProcessReportJob < ApplicationJob
  queue_as :default

  def perform(report_id)
    report = Report.find(report_id)
    # process...
  end
end

# Enqueue
ProcessReportJob.perform_later(report.id)
```

Configuration in `config/database.yml` under `queue` database.

---

## Solid Cache

Database-backed caching (replaces Redis/Memcached for caching).

```ruby
# Use Rails cache
Rails.cache.fetch("key", expires_in: 1.hour) do
  expensive_query
end
```

Configuration in `config/database.yml` under `cache` database.

---

## Solid Cable (Action Cable)

Database-backed Action Cable adapter for WebSocket support.

Configuration in `config/database.yml` under `cable` database.

---

## Database Configuration

PostgreSQL with three logical databases:

```yaml
# config/database.yml
production:
  primary:
    <<: *default
    database: myapp_production
  cache:
    <<: *default
    database: myapp_production_cache
    migrations_paths: db/cache_migrate
  queue:
    <<: *default
    database: myapp_production_queue
    migrations_paths: db/queue_migrate
```

---

## Routes (API Scoping)

```ruby
# config/routes.rb
Rails.application.routes.draw do
  # Health check
  get "up" => "rails/health#show", as: :rails_health_check

  # API endpoints
  scope path: :api do
    resources :posts
    resources :users
  end
end
```

---

## Kamal Deployment

Rails 8 uses Kamal for Docker-based deployment:

```bash
# Deploy
kamal deploy

# Setup (first time)
kamal setup

# Logs
kamal app logs
```

Configuration in `.kamal/` directory.

---

## Commands

```bash
# Server
bin/rails server

# Console
bin/rails console

# Database
bin/rails db:prepare
bin/rails db:migrate
bin/rails db:rollback

# Routes
bin/rails routes

# Generators
bin/rails generate migration CreatePosts title:string body:text
bin/rails generate model Post title:string body:text

# Zeitwerk
bin/rails zeitwerk:check
```
