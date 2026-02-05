---
name: rspec
description: >
  RSpec testing patterns for Ruby/Rails with FactoryBot.
  Trigger: When writing or refactoring RSpec tests (factories, request specs, matchers).
license: MIT
metadata:
  author: ""
  version: "1.0"
  scope: [root, spec]
  auto_invoke: "Writing RSpec tests"
---

## Basic Test Structure

```ruby
require 'rails_helper'

RSpec.describe "Feature", type: :request do
  describe "GET /api/resources" do
    it "returns all resources" do
      create_list(:resource, 3)
      get "/api/resources", as: :json

      expect(response).to have_http_status(:ok)
      json = JSON.parse(response.body)
      expect(json.size).to eq(3)
    end
  end
end
```

---

## FactoryBot

```ruby
# spec/factories/users.rb
FactoryBot.define do
  factory :user do
    sequence(:name) { |n| "User #{n}" }
    sequence(:email) { |n| "user#{n}@example.com" }
    role { "member" }

    trait :admin do
      role { "admin" }
    end
  end
end

# Usage in tests
create(:user)                    # Persisted record
create(:user, :admin)            # With trait
create_list(:user, 5)            # Multiple records
build(:user)                     # In-memory only (not saved)
attributes_for(:user)            # Hash of attributes
```

---

## Request Specs

```ruby
RSpec.describe "UsersController", type: :request do
  describe "POST /api/users" do
    it "creates a user and returns 201" do
      expect {
        post "/api/users",
             params: { name: "Alice", email: "alice@test.com" },
             as: :json
      }.to change(User, :count).by(1)

      expect(response).to have_http_status(:created)
      json = JSON.parse(response.body)
      expect(json["name"]).to eq("Alice")
    end
  end

  describe "PATCH /api/users/:id" do
    it "updates and returns 200" do
      user = create(:user, name: "Old")
      patch "/api/users/#{user.id}",
            params: { name: "New" },
            as: :json

      expect(response).to have_http_status(:ok)
      user.reload
      expect(user.name).to eq("New")
    end
  end
end
```

---

## Common Matchers

```ruby
# Status codes
expect(response).to have_http_status(:ok)
expect(response).to have_http_status(:created)
expect(response).to have_http_status(:not_found)

# Equality
expect(value).to eq(expected)
expect(value).to be_nil
expect(value).to be true

# Collections
expect(array).to match_array([1, 2, 3])  # Order-independent
expect(array).to all(be_a(String))
expect(array).to include("item")
expect(array.size).to eq(3)

# Changes
expect { action }.to change(Model, :count).by(1)
expect { action }.to change(Model, :count).by(-1)
expect { action }.not_to change(Model, :count)

# Types
expect(entity).to be_a(CommunityEntity)

# Exceptions
expect { action }.to raise_error(ActiveRecord::RecordNotFound)
```

---

## Model/Repository Specs

```ruby
RSpec.describe PostsRepository, type: :model do
  subject(:repo) { described_class.new }

  describe "#find" do
    it "returns entity when found" do
      record = create(:post)
      dto = IdDto.new(id: record.id)
      entity = repo.find(dto: dto)

      expect(entity&.id).to eq(record.id)
      expect(entity).to be_a(PostEntity)
    end

    it "returns nil when not found" do
      dto = IdDto.new(id: 0)
      expect(repo.find(dto: dto)).to be_nil
    end
  end
end
```

---

## Shared Examples

```ruby
# spec/support/shared_examples/crud_repository.rb
RSpec.shared_examples "a CRUD repository" do |factory_name, entity_class|
  subject(:repo) { described_class.new }

  describe "#find" do
    it "returns entity when found" do
      record = create(factory_name)
      entity = repo.find(dto: IdDto.new(id: record.id))
      expect(entity).to be_a(entity_class)
    end

    it "returns nil when not found" do
      expect(repo.find(dto: IdDto.new(id: 0))).to be_nil
    end
  end
end

# Usage
RSpec.describe CommunitiesRepository do
  it_behaves_like "a CRUD repository", :community, CommunityEntity
end
```

---

## Commands

```bash
bundle exec rspec                              # Run all tests
bundle exec rspec --format documentation       # Verbose output
bundle exec rspec spec/requests/               # Run request specs only
bundle exec rspec spec/repositories/           # Run repository specs only
bundle exec rspec spec/file_spec.rb:42         # Run specific line
bundle exec rspec --fail-fast                  # Stop on first failure
```
