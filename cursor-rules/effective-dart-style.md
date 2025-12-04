---
description: Dart style guide for consistent naming, ordering, and formatting. Use when writing or reviewing Dart code to ensure it follows official Effective Dart conventions.
---

# Effective Dart: Style

## When to Use

Apply this guide when:

- Writing new Dart code (classes, functions, files)
- Reviewing pull requests or code changes
- Refactoring existing code
- Generating code examples or snippets
- Setting up new Dart projects or packages

Consistent style makes code easier to read, understand, and maintain across the Dart ecosystem.

## Quick Reference

### Naming Patterns

| Element | Pattern | Example |
|---------|---------|---------|
| Classes, enums, typedefs | `UpperCamelCase` | `SliderMenu`, `HttpRequest` |
| Extensions | `UpperCamelCase` | `MyFancyList`, `SmartIterable` |
| Packages, directories, files | `lowercase_with_underscores` | `my_package`, `file_system.dart` |
| Import prefixes | `lowercase_with_underscores` | `as math`, `as angular_components` |
| Variables, methods, parameters | `lowerCamelCase` | `count`, `httpRequest`, `clearItems` |
| Constants | `lowerCamelCase` (preferred) | `pi`, `defaultTimeout` |

### Import Order Checklist

1. `dart:` imports (sorted alphabetically)
2. Blank line
3. `package:` imports (sorted alphabetically)
4. Blank line
5. Relative imports (sorted alphabetically)
6. Blank line
7. `export` statements (sorted alphabetically)

### Acronym Rules

- **3+ letters**: Treat as word → `Http`, `Nasa`, `Uri`
- **2 letters (capitalized in English)**: Keep caps → `ID`, `TV`, `UI`
- **2 letters (not capitalized)**: Treat as word → `Mr`, `St`, `Rd`
- **At start of lowerCamelCase**: All lowercase → `httpConnection`, `tvSet`, `mrRogers`

## Naming Conventions

### DO name types using `UpperCamelCase`

**Linter rule:** `camel_case_types`

Classes, enum types, typedefs, and type parameters should capitalize the first letter of each word (including the first word), and use no separators.

```dart
// Good
class SliderMenu {
  // ...
}

class HttpRequest {
  // ...
}

typedef Predicate<T> = bool Function(T value);
```

This includes classes used in metadata annotations:

```dart
// Good
class Foo {
  const Foo([Object? arg]);
}

@Foo(anArg)
class A {
  // ...
}

@Foo()
class B {
  // ...
}
```

For annotations with no parameters, create a separate `lowerCamelCase` constant:

```dart
// Good
const foo = Foo();

@foo
class C {
  // ...
}
```

### DO name extensions using `UpperCamelCase`

**Linter rule:** `camel_case_extensions`

Extensions follow the same convention as types:

```dart
// Good
extension MyFancyList<T> on List<T> {
  // ...
}

extension SmartIterable<T> on Iterable<T> {
  // ...
}
```

### DO name packages, directories, and source files using `lowercase_with_underscores`

**Linter rules:** `file_names`, `package_names`

File systems are often case-insensitive. Using lowercase with underscores keeps names readable and ensures they remain valid Dart identifiers.

```plaintext
// Good
my_package
└─ lib
   └─ file_system.dart
   └─ slider_menu.dart

// Bad
mypackage
└─ lib
   └─ file-system.dart
   └─ SliderMenu.dart
```

### DO name import prefixes using `lowercase_with_underscores`

**Linter rule:** `library_prefixes`

```dart
// Good
import 'dart:math' as math;
import 'package:angular_components/angular_components.dart' as angular_components;
import 'package:js/js.dart' as js;

// Bad
import 'dart:math' as Math;
import 'package:angular_components/angular_components.dart' as angularComponents;
import 'package:js/js.dart' as JS;
```

### DO name other identifiers using `lowerCamelCase`

**Linter rule:** `non_constant_identifier_names`

Class members, top-level definitions, variables, parameters, and named parameters should capitalize the first letter of each word *except* the first word.

```dart
// Good
var count = 3;

HttpRequest httpRequest;

void align(bool clearItems) {
  // ...
}
```

### PREFER using `lowerCamelCase` for constant names

**Linter rule:** `constant_identifier_names`

In new code, use `lowerCamelCase` for constant variables, including enum values.

```dart
// Good
const pi = 3.14;
const defaultTimeout = 1000;
final urlScheme = RegExp('^([a-z]+):');

class Dice {
  static final numberGenerator = Random();
}

// Bad
const PI = 3.14;
const DefaultTimeout = 1000;
final URL_SCHEME = RegExp('^([a-z]+):');

class Dice {
  static final NUMBER_GENERATOR = Random();
}
```

**Exception:** You may use `SCREAMING_CAPS` for consistency when:

- Adding code to a file that already uses `SCREAMING_CAPS`
- Generating Dart code parallel to Java code (e.g., protobufs)

### DO capitalize acronyms and abbreviations longer than two letters like words

Capitalized acronyms are hard to read. Multiple adjacent acronyms create ambiguous names. For example, `HTTPSFTP` is unclear—is it HTTPS FTP or HTTP SFTP?

**Rules:**

- Acronyms 3+ letters: Capitalize as words → `HttpsFtp` or `HttpSftp`
- Two-letter acronyms capitalized in English: Keep capitalized → `ID`, `TV`, `UI`
- Two-letter abbreviations not capitalized in English: Capitalize as words → `Mr`, `St`, `Rd`

```dart
// Good - Longer than two letters, treat as word
Http    // "hypertext transfer protocol"
Nasa    // "national aeronautics and space administration"
Uri     // "uniform resource identifier"
Esq     // "esquire"
Ave     // "avenue"

// Good - Two letters, capitalized in English
ID      // "identifier"
TV      // "television"
UI      // "user interface"

// Good - Two letters, not capitalized in English
Mr      // "mister"
St      // "street"
Rd      // "road"

// Bad
HTTP    // should be Http
NASA    // should be Nasa
URI     // should be Uri
esq     // should be Esq
ave     // should be Ave
Id      // should be ID
Tv      // should be TV
Ui      // should be UI
MR      // should be Mr
ST      // should be St
RD      // should be Rd
```

At the beginning of `lowerCamelCase` identifiers, all abbreviations are lowercase:

```dart
// Good
var httpConnection = connect();
var tvSet = Television();
var mrRogers = 'hello, neighbor';
```

### PREFER using wildcards for unused callback parameters

**Linter rules:** `no_wildcard_variable_uses`, `unnecessary_underscores`

When a callback doesn't use a parameter, name it `_` (wildcard variable, non-binding).

```dart
// Good
futureOfVoid.then((_) {
  print('Operation complete.');
});
```

Multiple unused parameters can all be named `_`:

```dart
// Good
.onError((_, _) {
  print('Operation failed.');
});
```

**Important:** This guideline is only for *anonymous and local* functions. Top-level functions and method declarations must use descriptive parameter names even if unused.

**Version note:** Wildcard variables require Dart 3.7+. In earlier versions, use `__`, `___`, etc.

### DON'T use a leading underscore for identifiers that aren't private

Dart uses leading underscores to mark members and top-level declarations as private. Using `_` on non-private elements (local variables, parameters, local functions, library prefixes) sends a confusing signal.

```dart
// Bad - local variable with leading underscore
void myFunction() {
  var _localVar = 42; // Confusing - looks private but isn't
}

// Good
void myFunction() {
  var localVar = 42;
}
```

### DON'T use prefix letters

Avoid Hungarian notation or similar schemes. Dart's type system provides type information, so encoding properties in names is unnecessary.

```dart
// Good
defaultTimeout

// Bad
kDefaultTimeout  // k prefix is unnecessary
```

### DON'T explicitly name libraries

The `library` directive with a name is a legacy feature. Dart generates unique tags based on file path and name.

```dart
// Bad
library my_library;

// Good
/// A really great test library.
@TestOn('browser')
library;
```

## Import and Export Ordering

**Linter rule:** `directives_ordering`

Keep file preambles tidy with this prescribed order. Separate each section with a blank line.

### DO place `dart:` imports before other imports

```dart
// Good
import 'dart:async';
import 'dart:collection';

import 'package:bar/bar.dart';
import 'package:foo/foo.dart';
```

### DO place `package:` imports before relative imports

```dart
// Good
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'util.dart';
```

### DO specify exports in a separate section after all imports

```dart
// Good
import 'src/error.dart';
import 'src/foo_bar.dart';

export 'src/error.dart';

// Bad
import 'src/error.dart';
export 'src/error.dart';
import 'src/foo_bar.dart';
```

### DO sort sections alphabetically

```dart
// Good
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'foo.dart';
import 'foo/foo.dart';

// Bad
import 'package:foo/foo.dart';
import 'package:bar/bar.dart';

import 'foo/foo.dart';
import 'foo.dart';
```

## Formatting

Consistent whitespace helps humans and compilers see code the same way.

### DO format your code using `dart format`

**The official whitespace rules for Dart are whatever `dart format` produces.**

The automated formatter handles tedious formatting work, especially during refactoring. Let it do the work for you.

The remaining guidelines cover things `dart format` cannot fix automatically.

### CONSIDER changing your code to make it more formatter-friendly

The formatter does its best, but it can't work miracles. If formatted output is hard to read due to long identifiers, deeply nested expressions, or mixed operators, reorganize your code:

- Shorten local variable names
- Hoist expressions into new variables
- Simplify complex logic

Think of `dart format` as a partnership—work together iteratively to produce beautiful code.

### PREFER lines 80 characters or fewer

**Linter rule:** `lines_longer_than_80_chars`

Long lines are harder to read because eyes travel farther to reach the next line. This is why newspapers use columns.

If you want lines longer than 80 characters, your code is likely too verbose. The main offender: `VeryLongCamelCaseClassNames`. Ask: "Does each word tell me something critical or prevent collision?" If not, omit it.

`dart format` defaults to 80 characters (configurable). It doesn't split long string literals—do that manually.

**Exceptions:**

1. URIs or file paths in comments/strings may exceed 80 characters (makes searching easier)
2. Multi-line strings can exceed 80 characters (newlines are significant)

### DO use curly braces for all flow control statements

**Linter rule:** `curly_braces_in_flow_control_structures`

This avoids the dangling else problem.

```dart
// Good
if (isWeekDay) {
  print('Bike to work!');
} else {
  print('Go dancing or read a book!');
}
```

**Exception:** An `if` statement with no `else` clause that fits on one line may omit braces:

```dart
// Good
if (arg == null) return defaultValue;
```

If the body wraps to the next line, use braces:

```dart
// Good
if (overflowChars != other.overflowChars) {
  return overflowChars < other.overflowChars;
}

// Bad
if (overflowChars != other.overflowChars)
  return overflowChars < other.overflowChars;
```

## AI Code Generation Guidelines

When generating or reviewing Dart code:

1. **Always run `dart format`** on generated code before presenting it
2. **Check naming patterns** against the quick reference table
3. **Verify import order** follows the prescribed structure
4. **Apply acronym rules** carefully—don't blindly capitalize or lowercase
5. **Use wildcards (`_`)** for unused callback parameters in anonymous functions
6. **Avoid prefix notation** like `k` or `m` prefixes
7. **Keep lines ≤80 characters** unless exceptions apply
8. **Include curly braces** on multi-line control structures
9. **Don't name libraries** unless absolutely necessary for backward compatibility

## Common Mistakes to Avoid

1. Using `SCREAMING_CAPS` for constants (prefer `lowerCamelCase`)
2. Mixing naming styles (`someVar`, `some_var`, `SomeVar` in same context)
3. Wrong import order (not grouping by `dart:`, `package:`, relative)
4. Using `HTTP`, `URL`, `ID` as words instead of `Http`, `Url`, `ID`
5. Adding leading underscores to local variables
6. Using Hungarian notation or other prefix schemes
7. Naming libraries explicitly with `library name;`
8. Omitting curly braces on multi-line if statements

## Reference

- [Effective Dart: Style](https://dart.dev/effective-dart/style)
- [Dart Formatter](https://dart.dev/tools/dart-format)
- [Linter Rules](https://dart.dev/tools/linter-rules)
- Key linter rules:
  - `camel_case_types`
  - `camel_case_extensions`
  - `file_names`, `package_names`
  - `library_prefixes`
  - `non_constant_identifier_names`
  - `constant_identifier_names`
  - `directives_ordering`
  - `lines_longer_than_80_chars`
  - `curly_braces_in_flow_control_structures`
