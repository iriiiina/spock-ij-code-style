# Spock IntelliJ Code Style

A shareable code style configuration for [Groovy](https://groovy-lang.org/)/[Spock](https://spockframework.org/) test projects in [IntelliJ IDEA](https://www.jetbrains.com/idea/). Drop these files into any project to get consistent formatting out of the box.

## What is included

| File | Purpose |
|------|---------|
| `.editorconfig` | Primary formatting rules using standard [EditorConfig](https://editorconfig.org/) properties plus IntelliJ-specific `ij_*` extensions. Covers Groovy, Java, XML, JSON, YAML, properties, shell scripts, and Markdown. |
| `.idea/codeStyles/codeStyleConfig.xml` | Tells IntelliJ to use the project-level code style instead of your personal IDE scheme. |
| `.idea/codeStyles/Project.xml` | Additional Groovy formatting rules that EditorConfig cannot express (e.g., blank line policies, brace placement, import layout). |

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

## Precedence

When both `.editorconfig` and `Project.xml` define the same formatting rule, `.editorconfig` takes precedence. The `Project.xml` file is only necessary for Groovy-specific settings that EditorConfig does not support.

## AI agent support

This repo includes a `CLAUDE.md` file with project context for [Claude Code](https://claude.com/product/claude-code) by Anthropic.

This repo was built and maintained with Claude Code (Claude Opus 4.6) -- from initial setup and file creation to branching, PRs, and documentation.

## License

[MIT](LICENSE)
