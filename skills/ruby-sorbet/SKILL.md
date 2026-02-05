---
name: ruby-sorbet
description: >
  Sorbet static typing patterns for Ruby.
  Trigger: When writing type signatures, T::Struct, generics, or fixing Sorbet errors.
license: MIT
metadata:
  author: ""
  version: "1.0"
  scope: [root, app]
  auto_invoke: "Writing Sorbet type signatures"
---

## Typed Sigils

| Sigil | Meaning | Use When |
|-------|---------|----------|
| `# typed: ignore` | Skip entirely | Generated files |
| `# typed: false` | No checking | Controllers (Rails magic) |
| `# typed: true` | Basic checking | Interactors, configs |
| `# typed: strict` | Full checking | DTOs, entities, repositories, models |

---

## Signatures (sig blocks)

```ruby
extend T::Sig

# Method with params and return
sig { params(name: String, age: Integer).returns(String) }
def greet(name:, age:)
  "Hello #{name}, age #{age}"
end

# Void return
sig { void }
def perform
  # ...
end

# Nilable return
sig { returns(T.nilable(String)) }
def maybe_name
  @name
end

# Abstract method
sig { abstract.returns(String) }
def required_method; end

# Override method
sig { override.returns(String) }
def required_method
  "implemented"
end

# Overridable method (can be overridden)
sig { overridable.params(dto: T.untyped).returns(T::Array[Entity]) }
def read(dto:)
  raise NotImplementedError
end
```

---

## T::Struct (Immutable Data Objects)

```ruby
# typed: strict

class UserEntity < T::Struct
  extend T::Sig

  const :id, Integer
  const :name, String
  const :email, String
  const :role, T.nilable(String)
end

# Usage
user = UserEntity.new(id: 1, name: "Alice", email: "alice@example.com", role: nil)
user.name  # => "Alice"
```

**Rules:**
- Use `const` for immutable fields (always preferred)
- Use `prop` only when mutation is genuinely needed
- DTOs and entities should always use `const`

---

## Common Types

```ruby
# Primitives
String, Integer, Float, T::Boolean, Symbol, NilClass

# Nilable
T.nilable(String)  # String or nil

# Arrays
T::Array[String]
T::Array[Integer]

# Hashes
T::Hash[String, Integer]
T::Hash[Symbol, T.untyped]

# Union types
T.any(String, Integer)

# Untyped (escape hatch)
T.untyped

# Must (assert non-nil)
T.must(value)  # raises if nil

# Let (typed variable assignment)
@repo = T.let(nil, T.nilable(Repository))
@repo ||= T.let(Repository.new, T.nilable(Repository))
```

---

## Generics

```ruby
class AbstractBaseRepository
  extend T::Sig
  extend T::Helpers
  extend T::Generic

  abstract!

  Entity = type_member { { upper: T::Struct } }

  sig { overridable.params(dto: IdDto).returns(T.nilable(Entity)) }
  def find(dto:)
    raise NotImplementedError
  end
end

class PostsRepository < AbstractBaseRepository
  Entity = type_member { { fixed: PostEntity } }

  sig { override.params(dto: IdDto).returns(T.nilable(Entity)) }
  def find(dto:)
    # ...
  end
end
```

---

## Abstract Classes

```ruby
class AbstractBase
  extend T::Sig
  extend T::Helpers

  abstract!

  sig { abstract.returns(String) }
  def name; end
end

class Concrete < AbstractBase
  sig { override.returns(String) }
  def name
    "concrete"
  end
end
```

---

## Commands

```bash
# Run type checker
bundle exec srb tc

# Check RBI freshness
bundle exec tapioca check

# Regenerate DSL RBIs
bundle exec tapioca dsl

# Regenerate gem RBIs
bundle exec tapioca gems

# Initialize Sorbet
bundle exec srb init
```
