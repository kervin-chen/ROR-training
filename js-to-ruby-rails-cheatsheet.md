# JavaScript (Node.js) → Ruby on Rails Rosetta Cheat Sheet

A fast, practical mapping from JS/Node idioms to Ruby/Rails syntax and conventions.

## Basics
- Semicolons: JS often uses `;` (ASI optional); Ruby omits `;`.
- Parentheses: JS needed in calls/ifs; Ruby can omit (`print "hi"`, `if cond`).
- Comments: JS `//`, `/* */`; Ruby `#`, `=begin`/`=end`.
- Naming: JS `camelCase`; Ruby `snake_case`. Ruby constants start uppercase (`MyConst`).

```javascript
// JS
let greeting = "hi"; // or const greeting = "hi";
if (user && user.isAdmin) {
  console.log(greeting);
}
```
```ruby
# Ruby
greeting = "hi"
if user && user.is_admin
  puts greeting
end
```

## Variables & Scope
- Declarations: JS `let/const/var`; Ruby locals (no keyword), plus `@instance`, `@@class`, `$global`.
- Mutability: JS `const` prevents rebinding; Ruby has no `const` for locals; constants warn on reassignment.
- Nil/Null: JS `null` and `undefined`; Ruby `nil`.
- Truthiness: JS falsy = `false, 0, "", null, undefined, NaN`; Ruby falsy = only `false, nil`.

```ruby
name = nil
puts "present" if name # won't run (nil is falsy)
puts "present" if 0    # runs (0 is truthy in Ruby)
```

## Functions/Methods & Blocks
- Define: JS `function` or arrow; Ruby `def` with optional defaults and keyword args.
- Return: JS usually explicit; Ruby returns last expression (implicit).
- Blocks: JS uses callbacks/arrow funcs; Ruby has blocks/procs/lambdas.
- Keyword args: JS via object destructuring; Ruby native `def f(a:, b: 1)`.

```javascript
// JS
function add(a = 1, b = 2) { return a + b; }
const doubled = [1,2,3].map(x => x * 2);

function greet({name, title = ""}) {
  return `${title} ${name}`.trim();
}
```
```ruby
# Ruby
def add(a = 1, b = 2)
  a + b
end

doubled = [1, 2, 3].map { |x| x * 2 }

def greet(name:, title: "")
  "#{title} #{name}".strip
end
```

## Equality & Comparison
- JS: `==` coerces, `===` strict; Ruby: `==` value equality, `eql?` value+type, `equal?` identity.
- Ruby `===` is case equality (used in `case/when`).
- Ruby spaceship `<=>` returns -1/0/1 for sorting.

```ruby
# Ruby case equality examples
(1..5) === 3        # true
/\A\w+\z/ === "abc"  # true
String === "hi"      # true
```

## Conditionals & Pattern Matching
- Else-if: JS `else if`; Ruby `elsif`.
- `unless`: Ruby negated `if`.
- Switch vs case: JS `switch/case`; Ruby `case/when` (supports ranges/types/regex).
- Ruby 3+ pattern matching: `case obj in pattern`.

```ruby
# Ruby
if admin?
  :ok
elsif staff?
  :limited
else
  :denied
end

case score
when 90..100 then :A
when 80..89  then :B
else :C
end

# Pattern matching (Ruby 3+)
case user
in {name:, role: "admin"}
  "Hello #{name}"
else
  "Hi"
end
```

## Safe Navigation & Coalescing
- Optional chaining: JS `obj?.a?.b`; Ruby `obj&.a&.b`.
- Nullish coalescing: JS `??`; Ruby typically `||` or `||=` idioms.

```ruby
user&.profile&.display_name || "Guest"
name ||= "Guest" # set if nil/false
```

## Ranges & Loops
- Ruby ranges: `1..5` inclusive, `1...5` exclusive.
- Iteration: JS `for/of`, `forEach`; Ruby `each`, `times`, `upto`, `downto`.

```ruby
(1..3).each { |i| puts i }
3.times { |i| puts i } # 0,1,2
```

## Strings & Symbols
- Interpolation: JS backticks `` `${x}` ``; Ruby double quotes `"#{x}"`.
- Symbols: Ruby `:symbol` (interned identifiers), common as hash keys.

```ruby
user = { name: "Ada", role: :admin }
"Hello, #{user[:name]}"
```

## Collections
- Objects vs Hashes: JS `{a: 1}`; Ruby `{ a: 1 }` (symbol keys) or `{"a" => 1}`.
- JS `map/filter/reduce/some/every`; Ruby `map/select/reduce/any?/all?/each_with_object`.

```javascript
// JS
const xs = [1,2,3]
  .filter(n => n % 2)
  .map(n => n * 10);
```
```ruby
# Ruby
xs = [1, 2, 3]
  .select { |n| n.odd? }
  .map { |n| n * 10 }
```

## Classes, Modules, Mixins
- Define: JS `class A {}`; Ruby `class A; end`.
- Inheritance: JS `extends`; Ruby `<`.
- Visibility: Ruby section-based `public/private/protected`.
- Accessors: Ruby `attr_reader/attr_writer/attr_accessor`.
- Mixins: Ruby `module M` + `include/extend`.

```javascript
// JS
class User {
  constructor(name) { this.name = name; }
  get display() { return this.name.toUpperCase(); }
  static version() { return 1; }
}
```
```ruby
# Ruby
module UpcaseName
  def display
    name.upcase
  end
end

class User
  include UpcaseName
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def self.version
    1
  end
end
```

## Errors & Exceptions
- JS `try/catch/finally` and `throw`; Ruby `begin/rescue/ensure` and `raise`.

```ruby
begin
  risky_call
rescue SomeError => e
  Rails.logger.error(e.message)
ensure
  cleanup
end
```

## Modules & Imports
- JS: `require`/`import`; Ruby: `require`/`require_relative`, namespacing via `module`.

```ruby
# Ruby
require "json"
require_relative "lib/util"

module MyApp
  class Runner; end
end
```

## Async & Concurrency
- JS: event loop, Promises, `async/await`.
- Ruby/Rails: mostly synchronous request cycle; threads/fibers exist; background work via ActiveJob/Sidekiq.

```ruby
# Rails background job
class ReportJob < ApplicationJob
  queue_as :default
  def perform(user_id)
    # heavy work here
  end
end
# Enqueue
ReportJob.perform_later(current_user.id)
```

## I/O & STDOUT
- JS `console.log`; Ruby `puts`, `print`, `p` (inspects).

## Regex
- Literals in both: `/\d+/`.
- JS `.test(str)`; Ruby `str =~ /\d+/` or `/\d+/.match(str)`.

## Rails DSL Highlights
- Routing:
```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :posts do
    resources :comments
  end
  get "/health", to: "health#show"
end
```
- Controllers (filters, params, instance vars to views):
```ruby
class PostsController < ApplicationController
  before_action :set_post, only: [:show, :edit, :update, :destroy]

  def index
    @posts = Post.published.order(created_at: :desc)
  end

  def show; end

  private
  def set_post
    @post = Post.find(params[:id])
  end
end
```
- Models/ActiveRecord (associations, validations, scopes, queries):
```ruby
class Post < ApplicationRecord
  has_many :comments, dependent: :destroy
  validates :title, presence: true
  scope :published, -> { where(published: true) }
end

Post.where(published: true).order(created_at: :desc).limit(10)
```
- Migrations:
```ruby
class CreatePosts < ActiveRecord::Migration[7.1]
  def change
    create_table :posts do |t|
      t.string :title, null: false
      t.text :body
      t.boolean :published, default: false
      t.timestamps
    end
  end
end
```
- Views (ERB):
```erb
<h1><%= @post.title %></h1>
<p><%= simple_format(@post.body) %></p>
<%= link_to "Back", posts_path %>
```

## CLI Mapping
- Start server: `bin/rails server` (alias: `rails s`).
- Console/REPL: `bin/rails console` (alias: `rails c`).
- DB tasks: `bin/rails db:create db:migrate db:seed`.
- Tests: built-in Minitest (`bin/rails test`) or RSpec (`bin/rspec`).
- Dependencies: Bundler manages gems via `Gemfile` (`bundle install`).

---

Tip: Think in “convention over configuration.” Let Rails naming and folder structure do the wiring (autoloading, routes, MVC). Prefer scopes, validations, and callbacks for expressive domain logic, and use background jobs for non-request-critical work.
