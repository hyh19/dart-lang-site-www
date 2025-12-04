---
description: Guidelines for writing clear, effective Dart documentation and comments following Effective Dart standards. Use when writing or reviewing Dart code documentation.
---

# Dart Documentation Guidelines

## When to Use

Apply these rules when:

- Writing doc comments for classes, functions, properties, or libraries
- Adding inline comments to Dart code
- Reviewing documentation in pull requests
- Generating new Dart code with documentation

## Quick Reference

| Rule Type | Guideline |
|-----------|-----------|
| **Comments** | Format like sentences with capitalization and periods |
| **Comments** | Use `//` not `/* */` for regular comments |
| **Doc Comments** | Use `///` not `//` for public API documentation |
| **Doc Comments** | Start with single-sentence summary, then blank line |
| **Functions (side effects)** | Start with third-person verbs (e.g., "Connects to...") |
| **Properties (non-boolean)** | Start with noun phrases (e.g., "The current...") |
| **Properties (boolean)** | Start with "Whether" (e.g., "Whether the modal...") |
| **Functions (returning value)** | Use noun phrases for conceptual properties |
| **Identifiers** | Wrap in `[brackets]` to create links |
| **Parameters** | Reference using `[paramName]`, not `@param` tags |
| **Code blocks** | Use triple backticks with language, not indentation |
| **Markdown** | Use sparingly; avoid HTML |

## Comments

### Format comments like sentences

**DO** capitalize the first word (unless case-sensitive identifier) and end with period.

```dart
// Good
// Not if anything comes before it.
if (_chunks.isNotEmpty) return false;

// Bad
// not if anything comes before it
if (_chunks.isNotEmpty) return false;
```

### Use `//` for inline comments

**DON'T** use block comments `/* */` except to temporarily comment out code.

```dart
// Good
void greet(String name) {
  // Assume we have a valid name.
  print('Hi, $name!');
}

// Bad
void greet(String name) {
  /* Assume we have a valid name. */
  print('Hi, $name!');
}
```

## Doc Comments

### Use `///` for documentation

**DO** use `///` for all public API documentation. This enables `dart doc` to generate documentation.

```dart
// Good
/// The number of characters in this chunk when unsplit.
int get length => ...

// Bad
// The number of characters in this chunk when unsplit.
int get length => ...
```

### Document public APIs

**PREFER** writing doc comments for most public libraries, types, and members.

**CONSIDER** writing doc comments for private APIs when they help understanding.

### Library-level documentation

**CONSIDER** writing library-level doc comments that include:

- Single-sentence summary of the library's purpose
- Terminology explanations
- Code samples
- Links to important classes/functions

```dart
/// A really great test library.
@TestOn('browser')
library;
```

## Doc Comment Structure

### Start with single-sentence summary

**DO** begin with a brief, user-centric sentence ending with a period.

```dart
// Good
/// Deletes the file at [path] from the file system.
void delete(String path) { ... }

// Bad (too much context upfront)
/// Depending on the state of the file system and the user's permissions,
/// certain operations may or may not be possible. If there is no file at
/// [path] or it can't be accessed, this function throws either [IOError]
/// or [PermissionError], respectively. Otherwise, this deletes the file.
void delete(String path) { ... }
```

### Separate first sentence into its own paragraph

**DO** add a blank line after the first sentence. Tools use this as a summary.

```dart
// Good
/// Deletes the file at [path].
///
/// Throws an [IOError] if the file could not be found. Throws a
/// [PermissionError] if the file is present but could not be deleted.
void delete(String path) { ... }

// Bad (no separation)
/// Deletes the file at [path]. Throws an [IOError] if the file could not
/// be found. Throws a [PermissionError] if the file is present but could
/// not be deleted.
void delete(String path) { ... }
```

### Avoid redundancy

**AVOID** repeating information visible in the declaration. Focus on what isn't obvious.

```dart
// Good
class RadioButtonWidget extends Widget {
  /// Sets the tooltip to [lines].
  ///
  /// The lines should be word wrapped using the current font.
  void tooltip(List<String> lines) { ... }
}

// Bad (repeats class name and parameter type unnecessarily)
class RadioButtonWidget extends Widget {
  /// Sets the tooltip for this radio button widget to the list of strings in [lines].
  void tooltip(List<String> lines) { ... }
}
```

If nothing interesting can be said beyond the declaration, **omit the doc comment**.

## Documentation by Declaration Type

### Functions with side effects

**PREFER** starting with third-person verbs describing what the code does.

```dart
/// Connects to the server and fetches the query results.
Stream<QueryResult> fetchResults(Query query) => ...

/// Starts the stopwatch if not already running.
void start() => ...
```

### Non-boolean properties and variables

**PREFER** starting with noun phrases describing what the property is.

```dart
/// The current day of the week, where `0` is Sunday.
int weekday;

/// The number of checked buttons on the page.
int get checkedCount => ...
```

### Boolean properties and variables

**PREFER** starting with "Whether" followed by a noun or gerund phrase.

```dart
/// Whether the modal is currently displayed to the user.
bool isVisible;

/// Whether the modal should confirm the user's intent on navigation.
bool get shouldConfirm => ...

/// Whether resizing the current browser window will also resize the modal.
bool get canResize => ...
```

**Note:** Avoid "Whether or not" - just "Whether" is sufficient.

### Functions returning values

**PREFER** noun phrases for methods that are conceptually properties.

```dart
/// The [index]th element of this iterable in iteration order.
E elementAt(int index);

/// Whether this iterable contains an element equal to [element].
bool contains(Object? element);
```

### Getters and setters

**DON'T** write documentation for both getter and setter. Document only the getter.

```dart
// Good
/// The pH level of the water in the pool.
///
/// Ranges from 0-14, representing acidic to basic, with 7 being neutral.
int get phLevel => ...
set phLevel(int level) => ...

// Bad
/// The depth of the water in the pool, in meters.
int get waterDepth => ...

/// Updates the water depth to a total of [meters] in height.
set waterDepth(int meters) => ...
```

### Classes and libraries

**PREFER** starting with noun phrases describing an instance of the type.

```dart
/// A chunk of non-breaking output text terminated by a hard or soft newline.
///
/// ...
class Chunk { ... }
```

## Code Samples and References

### Include code samples

**CONSIDER** including code samples to illustrate usage.

````dart
/// The lesser of two numbers.
///
/// ```dart
/// min(5, 3) == 3
/// ```
num min(num a, num b) => ...
````

### Use square brackets for references

**DO** use `[identifier]` to link to types, methods, or variables. `dart doc` creates links.

```dart
/// Throws a [StateError] if ...
///
/// Similar to [anotherMethod()], but ...
```

Reference class members with dot notation:

```dart
/// Similar to [Duration.inDays], but handles fractional days.
```

Reference constructors:

```dart
/// To create a point, call [Point.new] or use [Point.polar] to ...
```

### Document parameters and exceptions with prose

**DO** integrate parameter and exception documentation into prose using `[paramName]`.

**DON'T** use `@param`, `@returns`, or `@throws` tags.

```dart
// Good
/// Defines a flag with the given [name] and [abbreviation].
///
/// The [name] and [abbreviation] strings must not be empty.
///
/// Returns a new flag.
///
/// Throws a [DuplicateFlagException] if there is already an option named
/// [name] or there is already an option using the [abbreviation].
Flag addFlag(String name, String abbreviation) => ...

// Bad
/// Defines a flag with the given name and abbreviation.
///
/// @param name The name of the flag.
/// @param abbr The abbreviation for the flag.
/// @returns The new flag.
/// @throws ArgumentError If there is already an option with
///     the given name or abbreviation.
Flag addFlag(String name, String abbreviation) => ...
```

### Put doc comments before annotations

**DO** place doc comments before metadata annotations.

```dart
// Good
/// A button that can be flipped on and off.
@Component(selector: 'toggle')
class ToggleComponent {}

// Bad
@Component(selector: 'toggle')
/// A button that can be flipped on and off.
class ToggleComponent {}
```

## Markdown Formatting

### Use backtick fences for code blocks

**PREFER** triple backticks over indentation for code blocks.

```dart
// Good
/// You can use [CodeBlockExample] like this:
///
/// ```dart
/// var example = CodeBlockExample();
/// print(example.isItGreat); // "Yes."
/// ```

// Bad
/// You can use [CodeBlockExample] like this:
///
///     var example = CodeBlockExample();
///     print(example.isItGreat); // "Yes."
```

### Supported Markdown

You can use:

- `*italic*` or `_italic_` for emphasis
- `**bold**` or `__bold__` for strong emphasis
- `` `inline code` `` for code
- Unordered lists with `*`, `-`, or `+`
- Numbered lists with `1.`, `2.`, etc.
- Links: `[text](url)` or `[text][ref]` with `[ref]: url`
- Headers: `#`, `##`, `###`, etc.

Example:

```dart
/// This is a paragraph of regular text.
///
/// This sentence has *emphasized* words and **strong** ones.
///
/// A blank line creates a separate paragraph. It has some `inline code`.
///
/// * Unordered lists.
/// * Look like ASCII bullet lists.
///
/// 1. Numbered lists.
/// 2. Are numbered.
///
/// Code blocks are fenced in triple backticks:
///
/// ```dart
/// this.code.will.retain(its, formatting);
/// ```
///
/// Links: [with the URL inline](https://example.com)
```

### Avoid excessive formatting

**AVOID** using markdown excessively. Format to illuminate content, not replace it.

**AVOID** using HTML for formatting. Use markdown instead.

## Writing Style

### Be brief

**PREFER** clear, precise, and terse documentation.

### Avoid uncommon abbreviations

**AVOID** abbreviations like "i.e.", "e.g.", "et al." unless they're obvious. Spell out acronyms that may not be widely known.

### Use "this" for instance references

**PREFER** using "this" instead of "the" when referring to the instance.

```dart
class Box {
  /// The value this box wraps.
  Object? _value;

  /// Whether this box contains a value.
  bool get hasValue => _value != null;
}
```

## Common Patterns

### Documenting exceptions

Structure: First sentence summary, then blank line, then "Throws [ExceptionType] if..."

```dart
/// Deletes the file at [path].
///
/// Throws an [IOError] if the file could not be found.
void delete(String path) { ... }
```

### Documenting return values

Structure: First sentence summary, then blank line, then "Returns..."

```dart
/// Parses [source] as a JSON string.
///
/// Returns the decoded JSON value.
dynamic jsonDecode(String source) { ... }
```

### Documenting requirements

Structure: Mention requirements after the blank line following the summary.

```dart
/// Creates a user with the given [name].
///
/// The [name] must not be empty and must be unique.
User createUser(String name) { ... }
```

## Examples to Follow

**Complete function documentation:**

```dart
/// Connects to the database at [host] using [credentials].
///
/// The [host] must be a valid hostname or IP address. The [credentials]
/// must include both username and password.
///
/// Returns a [Connection] instance if successful.
///
/// Throws a [ConnectionException] if the connection fails.
Future<Connection> connect(String host, Credentials credentials) async { ... }
```

**Complete class documentation:**

```dart
/// A connection to a remote database.
///
/// Use [connect] to create a connection. Remember to call [close] when done
/// to release resources.
///
/// Example:
///
/// ```dart
/// final conn = await connect('localhost', credentials);
/// try {
///   await conn.query('SELECT * FROM users');
/// } finally {
///   await conn.close();
/// }
/// ```
class Connection { ... }
```

## Reference

Based on [Effective Dart: Documentation](https://dart.dev/effective-dart/documentation)
