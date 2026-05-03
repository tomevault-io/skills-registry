---
name: rspec-gary
description: > Use when this capability is needed.
metadata:
  author: scottmatthewman
---

# RSpec Testing: Be More GARY

When writing RSpec tests, follow the **GARY** principle: **Go Ahead, Repeat Yourself**.

While executable code should be DRY, test code *describes*, *verifies*, and *documents* system knowledge.
Prioritise **readability over brevity**.

## When to use this skill

Use when the user wants to write RSpec tests that are easy to read, understand, and maintain.

## Core principles

Every test tells a story using Arrange-Act-Assert:

```ruby
# Arrange - set up the world
user = create(:user)
post = create(:post, author: user)

# Act - perform the action
post.publish!

# Assert - verify the outcome
expect(post).to be_published
```

A reader should understand the test **top to bottom** without hunting for `let` or `before` blocks.

## Guidelines

### 1. Prefer inline setup over `let`

`let` statements create invisible dependencies and force readers to jump around:

```ruby
# Avoid: Reader must hunt for definitions, lazy evaluation hides dependencies
let(:jon) { create(:person) }
let(:garfield) { create(:cat, owner: jon) }
let(:odie) { create(:dog, owner: jon) }

it 'has daily fights when pets are enemies' do
  # Fails! garfield and odie never instantiated due to lazy evaluation
  expect(Household.new(jon).daily_fights.size).to be_positive
end
```

```ruby
# Prefer: Self-contained, linear story
it 'has daily fights when pets are enemies' do
  jon = create(:person)
  garfield = create(:cat, owner: jon)
  odie = create(:dog, owner: jon)
  garfield.enemies << odie

  expect(Household.new(jon).daily_fights.size).to be_positive
end
```

**Never override `let` in nested contexts** - it forces readers to trace inheritance chains.

### 2. Extract only narrative noise

Values required but irrelevant to the test's story can be extracted:

```ruby
# OK: valid_oauth_token is noise, not part of this test's story
let(:valid_oauth_token) { 'abc123...' }

it 'returns the user profile for a valid request' do
  user = create(:user, name: 'Alice')

  get '/api/profile', headers: { 'Authorization' => "Bearer #{valid_oauth_token}" }

  expect(response.parsed_body['name']).to eq('Alice')
end
```

If a value is central to understanding the test, keep it inline even if repeated.

### 3. Keep actions in the test body

Don't hide actions in `before` blocks or factories - everything being asserted should be performed in the test itself.

```ruby
# Avoid:
before { post.publish! }  # Action hidden

# Prefer: Action visible in test
it 'publishes the post' do
  post = create(:post)
  post.publish!
  expect(post).to be_published
end
```

### 4. Name variables for context

```ruby
it 'rejects expired tokens' do
  expired_token = create(:oauth_token, expires_at: 1.day.ago)

  get '/api/profile', headers: auth_header(expired_token)

  expect(response).to have_http_status(:unauthorized)
end
```

### 5. Control values, don't extract them

```ruby
# Avoid: Extracting generated values
voip_session = create(:voip_session)
phone_number = voip_session.psid.phone_number

# Prefer: Controlling the value
phone_number = "+447001234567"
voip_session = create(:voip_session, phone_number: phone_number)
```

IDs may be an exception when code requires IDs rather than objects.

### 6. Use helper methods for complex setup

Keep helpers close to the specs that use them:

```ruby
def build_classroom_with_students(student_count:)
  school = create(:school)
  classroom = create(:classroom, school: school)
  create_list(:student, student_count, classroom: classroom)
  classroom
end

it 'calculates average reading level' do
  classroom = build_classroom_with_students(student_count: 5)
  expect(classroom.average_reading_level).to be_present
end
```

## When repetition becomes a problem

If copying 10+ lines across many tests, consider:
1. **Helper methods in the spec file** - keep them close
2. **Shared examples** - only for genuinely shared *behaviour*, not shared *setup*
3. **Custom matchers** - for complex assertions

Always ask: does extraction make each test easier or harder to understand in isolation?

**The test:** Can a developer unfamiliar with this code understand the test in one read-through?
When this test fails in 2 years, will the error message and test code make the problem obvious?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scottmatthewman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
