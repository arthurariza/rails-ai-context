<ruby-on-rails-guidelines>
=== foundation rules ===

# Ruby On Rails Guidelines

These guidelines should be followed closely to enhance the user's satisfaction building Ruby On Rails applications.

## Foundational Context
This application is a Ruby On Rails application and its main packages & versions are below. You are an expert with them all. Ensure you abide by these specific packages & versions.

- ruby - 3.4
- ruby_on_rails - v8

## Conventions
- You must follow all existing code conventions used in this application. When creating or editing a file, check sibling files for the correct structure, approach, naming.
- Check for existing components to reuse before writing a new one.

## Application Structure & Architecture
- Stick to existing directory structure - don't create new base folders without approval.
- Do not change the application's dependencies without approval.

## Replies
- Be concise in your explanations - focus on what's important rather than explaining obvious details.

## Documentation Files
- You must only create documentation files if explicitly requested by the user.

=== mcp rules ===

## Tidewave
- Tidewave is an MCP server that comes with powerful tools designed specifically for this application. Use them.

### Project Eval
- Use the `project_eval` tool every time you need to evaluate Ruby code, including to test the behaviour of a function or to debug something. The tool also returns anything written to standard output. DO NOT use shell tools to evaluate Ruby code.

### Get Models
- Use the `get_models`tool to return a list of all database-backed models in the application.

### Get Logs
- Use the `get_logs` tool to check for request logs or potentially logged errors. Returns all log output, excluding logs that were caused by other tool calls.

### Get Docs
- Returns the documentation for the given reference. The reference may be a constant, most commonly classes and modules such as `String`, an instance method, such as `String#gsub`, or class method, such as `File.executable?` This works for methods in the current project, as well as dependencies. This tool only works if you know the specific constant/method being targeted. If that is the case, prefer this tool over grepping the file system.

### Execute Sql Query
- Use the `execute_sql_query` tool to select user data, manipulate entries, and introspect the application data domain. Executes the given SQL query against the database connection. Returns the result as a Ruby data structure.

## Playwright MCP
- Playwright mcp is available and can be used to interact with the browser, specially usefull when developing the frontend.

=== ruby rules ===

## Ruby Rules

### Class Structure
- **Key word arguments:** Use key word arguments for the initializer method
- **Dependcy Injection:** Inject dependencies in the initializer with key word arguments
- **Duck Typing:** Prefer duck-typing over inheritance

### Control Flow
- **Happy path last**: Handle error conditions first, success case last
- **Avoid else**: Use early returns instead of nested conditions  
- **Separate conditions**: Prefer multiple if statements over compound conditions

### Comments
- **No comments:** - Write self-documenting code

=== rails rules ===

## Rails Rules
- **Follow Ruby On Rails conventions first**: If Rails has a documented way to do something, use it. Only deviate when you have a clear justification.
- Use `bin/rails generate` commands to create new files (i.e. migrations, controllers, models, etc.).

### Controllers
- Plural resource names (`posts_controller`)
- Stick to CRUD methods (`index`, `show`, `new`, `create`, `edit`, `update`, `destroy`)
- Extract new controllers for non-CRUD actions

### Models
- **Model Design**: Create well-structured ActiveRecord models with appropriate validations
- **Associations**: Define relationships between models (has_many, belongs_to, has_and_belongs_to_many, etc.)
- **Migrations**: Write safe, reversible database migrations
- **Query Optimization**: Implement efficient scopes and query methods
- **Database Design**: Ensure proper normalization and indexing
- **Namespace Models**: Ensure related models are nested under the parent
- **Model callbacks:** Callbacks should be used only as a last resort

### Model Concerns Organization

#### Location Pattern
- Use `app/models/[model_name]/[feature].rb` NOT `app/models/concerns/`
- Example: `app/models/user/avatar.rb`, `app/models/message/searchable.rb`

#### Naming Convention
- Namespace: `ModelName::FeatureName`
- Name by capability: `Avatar`, `Searchable`, `Connectable`, `Joinable`
- NOT by entity: `UserAvatar`, `MessageSearch`

#### Implementation Types

##### Complex concerns - use ActiveSupport::Concern
<code-snippet name="Complex concerns" lang="ruby">
module User::Bot
  extend ActiveSupport::Concern

  included do
    # callbacks, associations, scopes
  end

  class_methods do
    # class methods
  end

  # instance methods
end
</code-snippet>

##### Simple concerns - plain modules
<code-snippet name="Simple concerns" lang="ruby">
module Message::Broadcasts
  def broadcast_create
    # simple behavior
  end
end
</code-snippet>

##### Mixed concerns - include other modules
<code-snippet name="Mixed concerns" lang="ruby">
module User::Mentionable
  include ActionText::Attachable
  # additional behavior
end
</code-snippet>

### Business Logic Separation

#### Model Concerns Handle:
- Model-specific behavior and capabilities
- Lifecycle callbacks and validations
- Scopes and associations
- Feature toggles and state

#### Service Objects Handle:
- Complex workflows spanning multiple models
- External API integration
- Multi-step business processes
- Place in `app/models/[domain]/[action].rb` (e.g., `Room::MessagePusher`)

#### Controllers Handle:
- Request-specific business logic
- Authorization and permissions
- Broadcasting and real-time updates
- Coordination between models

### Model Inclusion Pattern

Always explicitly include concerns in the model:
<code-snippet name="Model Inclusion Pattern" lang="ruby">
class User < ApplicationRecord
  include Avatar, Bot, Mentionable, Role, Transferable
  # rest of model
end
</code-snippet>

### When to Create New Concerns

#### Create a concern when:
- Adding a distinct capability to a model (e.g., search, avatars, connections)
- Behavior is model-specific and reusable
- Code would otherwise clutter the main model file

#### Don't create a concern when:
- Logic spans multiple models → use service object
- Simple one-off methods → keep in main model
- Infrastructure logic → use lib/ classes


=== rspec rules ===

## Rspec rules
- Every time a test has been updated, run that singular test.
- When the tests relating to your feature are passing, ask the user if they would like to also run the entire test suite to make sure everything is still passing.
- You must not remove any tests or test files from the tests directory without approval. These are not temporary or helper files, these are core to the application.

### Arrange-Act-Assert
1. **Arrange**: Set up test data and prerequisites
2. **Act**: Execute the code being tested
3. **Assert**: Verify the expected outcome

### Test Data
- Use factories (FactoryBot) or fixtures
- Create minimal data needed for each test
- Avoid dependencies between tests
- Clean up after tests

### Edge Cases
- **Handling Empty Datasets:** Ensure code handles empty datasets gracefully.
- **Handling Large Datasets:** Optimize code to handle large datasets efficiently.
- **Handling Time Zones:** Be aware of time zone issues when working with dates and times.
- **Handling Exceptions:** Failing to handle exceptions can cause the application to crash.
- **Invalid inputs**: Test invalid input, never trust user input

### Coverage Guidelines
- Aim for high coverage but focus on meaningful tests
- Test all public methods
- Test edge cases and error conditions
- Don't test Rails framework itself
- Focus on business logic coverage

### Rspec Anti Patterns
- Never use **let!**
- Chaining to many context blocks
- Never use `allow_any_instance_of` use `allow(Class).to_receive(:method).and_return(instance)` pattern

### Rspec Good Pattern
- **Describe Your Methods**: Be clear about what method you are describing.
- **Keep your description short**: A spec description should never be longer than 40 characters. If this happens you should split it using a context.
- **Single expectation test**: This helps you on finding possible errors, going directly to the failing test, and to make your code readable. In isolated unit specs, you want each example to specify one (and only one) behavior. Multiple expectations in the same example are a signal that you may be specifying multiple behaviors.
- **Test all possible cases**: Testing is a good practice, but if you do not test the edge cases, it will not be useful. Test valid, edge and invalid case.
- **Expect Syntax**: Always use the expect syntax.
- **Create only the data you need**

=== rubocop rules ===

## Rubocop Code Formatter

- You must run `bundle exec rubocop -a` before finalizing changes to ensure your code matches the project's expected style.

</ruby-on-rails-guidelines>
