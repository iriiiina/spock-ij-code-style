# Spock IntelliJ Code Style

[![License: MIT](https://img.shields.io/github/license/iriiiina/spock-ij-code-style)](LICENSE)
[![GitHub last commit](https://img.shields.io/github/last-commit/iriiiina/spock-ij-code-style)](https://github.com/iriiiina/spock-ij-code-style/commits/main)
[![GitHub stars](https://img.shields.io/github/stars/iriiiina/spock-ij-code-style)](https://github.com/iriiiina/spock-ij-code-style/stargazers)
[![EditorConfig](https://img.shields.io/badge/EditorConfig-supported-blue)](https://editorconfig.org/)
[![IntelliJ IDEA](https://img.shields.io/badge/IntelliJ%20IDEA-compatible-purple)](https://www.jetbrains.com/idea/)
[![Claude](https://img.shields.io/badge/Claude-compatible-D97706?logo=claude)](CLAUDE.md)

A shareable code style configuration for [Groovy](https://groovy-lang.org/)/[Spock](https://spockframework.org/) test projects in [IntelliJ IDEA](https://www.jetbrains.com/idea/). Drop these files into any project to get consistent formatting out of the box.

> **Using an AI coding assistant?** See [Using Claude Code in your project](#using-claude-code-in-your-project) for a ready-to-paste `CLAUDE.md` snippet with Groovy/Spock conventions.

## What is included

| File | Purpose |
|------|---------|
| `.editorconfig` | Primary formatting rules using standard [EditorConfig](https://editorconfig.org/) properties plus IntelliJ-specific `ij_*` extensions. Covers Groovy, Java, XML, JSON, YAML, properties, shell scripts, and Markdown. |
| `.idea/codeStyles/codeStyleConfig.xml` | Tells IntelliJ to use the project-level code style instead of your personal IDE scheme. |
| `.idea/codeStyles/Project.xml` | Additional Groovy formatting rules that EditorConfig cannot express (e.g., blank line policies, brace placement, import layout). |

### Precedence

When both `.editorconfig` and `Project.xml` define the same formatting rule, `.editorconfig` takes precedence. The `Project.xml` file is only necessary for Groovy-specific settings that EditorConfig does not support.

## How to use

1. Copy the three files into the root of your project, preserving the directory structure.
2. Open (or reopen) the project in IntelliJ IDEA.
3. IntelliJ picks up the settings automatically -- no manual import required.

## .gitignore setup

Most projects ignore the entire `.idea/` directory. To commit the code style files while still ignoring everything else under `.idea/`, the parent `.gitignore` must use a glob pattern instead of a directory pattern:

```gitignore
# Use .idea/* (glob), NOT .idea/ (directory).
# Directory-level ignores cannot be overridden by child negation patterns.
.idea/*

# Allow the code style files through
!.idea/codeStyles/
```

If your `.gitignore` uses `.idea/` (with a trailing slash), Git treats it as a directory ignore and the negation pattern `!.idea/codeStyles/` will have no effect.

## Key Groovy formatting rules

| Rule | Value |
|------|-------|
| Indent style | tabs |
| Indent size | 2 |
| Continuation indent | 4 |
| Max line length | 120 |
| Braces | forced (always required) |
| Final newline | yes |
| Trailing whitespace | trimmed |

See `.editorconfig` for the full set of rules and per-language overrides.

## Examples

Basic test with `given/when/then` and forced braces:

```groovy
class UserServiceSpec extends Specification {

	def "should register new user and send welcome email"() {
		given:
		def user = new User(name: "Jane Doe", email: "jane@example.com")

		and:
		userRepository.findByEmail(user.email) >> null

		when:
		def result = userService.register(user)

		then:
		1 * userRepository.save({ User saved ->
			saved.name == "Jane Doe"
			saved.createdAt != null
		}) >> user

		and:
		1 * emailService.sendWelcome(user.email)

		and:
		result.name == "Jane Doe"
	}
}
```

Data-driven test with `where:` block:

```groovy
class EmailValidationSpec extends Specification {

	def "should validate email format for '#email'"() {
		expect:
		emailService.isValid(email) == expected

		where:
		email               || expected
		"user@example.com"  || true
		"admin@company.org" || true
		"invalid-email"     || false
		""                  || false
	}
}
```

Aligned field declarations with Selenide page objects:

```groovy
class CheckoutPage {

	static  String             url        = "${Conf.baseUrl}/checkout"
	private SelenideElement    cartTotal  = $(cssSelector: "[data-testid=cartTotal]")
	        ElementsCollection orderItems = $$(cssSelector: "[data-testid=orderItem]")
}
```

Exception handling and collection assertions:

```groovy
class OrderServiceSpec extends Specification {

	def "should throw exception when order not found"() {
		given:
		orderRepository.findById(999L) >> Optional.empty()

		when:
		orderService.getById(999L)

		then:
		thrown(OrderNotFoundException)
	}

	def "should filter orders by status"() {
		given:
		def orders = [
			new Order(id: 1, status: SHIPPED, total: 149.50),
			new Order(id: 2, status: PENDING, total: 29.99),
			new Order(id: 3, status: SHIPPED, total: 200.00),
		]

		when:
		def result = orderService.filterByStatus(orders, SHIPPED)

		then:
		result.size() == 2
		result.every { it.status == SHIPPED }
		result*.total.sum() == 349.50
	}
}
```

## Using Claude Code in your project

[Claude Code](https://claude.com/product/claude-code) uses a `CLAUDE.md` file in the project root to understand your project's conventions. It works like a persistent prompt: Claude picks it up automatically and applies its rules to every suggestion, refactor, and code generation it makes.

Run `/init` inside Claude Code to auto-generate a starter `CLAUDE.md` from your existing project. Then paste the block below to add Groovy/Spock rules that match this code style config:

> **Note:** This snippet covers only the Groovy/Spock-specific part. A complete `CLAUDE.md` should also describe your project's architecture, build commands, domain conventions, and anything else Claude needs to work effectively in your codebase.

````markdown
## Coding Standards

### Formatting

All formatting rules are defined in `.editorconfig` and `.idea/codeStyles/Project.xml`. When writing or editing code, follow these key rules:

- **Groovy**: use tabs (width 2), max line length 120, continuation indent 4
- **YAML**: use spaces (indent size 2)
- **Properties**: use `=` delimiter, preserve blank lines between groups
- **XML/POM**: use spaces (indent size 4), align attributes
- **Markdown**: wrap text if long, one blank line around headers and block elements

### Groovy Style

- Use precise variable types instead of `def` when the type is known:
  ```groovy
  // BAD
  def session = new Session()

  // GOOD
  Session session = new Session()
  ```

### Test Structure (Spock BDD)

- In test classes, all test methods should come first, followed by helper methods at the bottom of the class
- The first test in a class should always be the happy-path test
- When adding a new test, add it as the last test method in the class (unless it's a happy-path test, which goes first).
  Helper methods must remain at the very bottom, after all tests.
- Remove unused imports, variables, and methods after editing code
- If deleting code leaves a Spock block empty (`given:`, `when:`, `then:`, `and:`, `cleanup:`), delete the block label
  too
- When adding a label to a Spock block (e.g. `when: "..."`, `and: "..."`), make sure the description accurately reflects
  what that block actually does

All tests follow this pattern:

```groovy
@Epic("UI") // Test layer: UI, Accessibility, API. API tests have module name as well, like "API: PERSON"
@Feature("Authentication") // The main purpose of the current test suit. UI tests describe flow here, API tests contains endpoint under test 
@Timeout(value = 1, unit = TimeUnit.MINUTES) // UI tests have larger timeouts, like 1 or even more minutes, API tests have 30 seconds timeout, health monitoring tests have 10 second timeout
class MyTest extends Spec {
    
    @Ignore("TASK NUMBER") // If some test is ignored, then it should be related to some task number
    @Story("REQUIREMENT_ID")
    @Link(name = "REQUIREMENT_ID", url = "https://documentation/REQUIREMENT_ID")
    def "test description"() {
        given:
        // Setup session, login page, test data

        when:
        // Perform actions

        then:
        // Assertions

        cleanup:
        // Optional cookie deletion, clear storage (mostly in UI tests)

        where:  // Optional parameterized tests
        param1 | param2 || expected
        "val1" | "val2" || "result"
    }
}
```
````

## AI agent support of this repo

This repo includes a [`CLAUDE.md`](CLAUDE.md) file with project context for Claude Code.

This repo was built and maintained with Claude Code (Claude Opus 4.6) - from initial setup and file creation to branching, PRs, and documentation.

## License

[MIT](LICENSE)
