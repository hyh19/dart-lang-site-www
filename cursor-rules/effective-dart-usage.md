---
description: Guidelines for using Dart language features to write maintainable code. Covers libraries, null safety, strings, collections, functions, variables, members, constructors, error handling, and asynchrony.
---

# Effective Dart: Usage

## When to Use

Apply these guidelines when writing Dart code bodies and implementing functionality. These rules help you:

- Compose programs from multiple files consistently
- Handle null safety and nullable types correctly
- Work with strings, collections, and functions idiomatically
- Design classes with proper constructors and members
- Handle errors and asynchronous operations effectively

## Libraries

### DO use strings in `part of` directives

**Linter:** `use_string_in_part_of_directives`. Use `part of '../../my_library.dart';` not `part of my_library;`

### DON'T import from another package's `src`

**Linter:** `implementation_imports`. Private code can change without version bump.

### DON'T reach into/out of `lib`

**Linter:** `avoid_relative_lib_imports`. Use `package:` imports, not `../lib/`.

### PREFER relative imports

**Linter:** `prefer_relative_imports`. Within `lib`, use `import '../api.dart';`

## Null Safety

### DON'T explicitly initialize to `null`

**Linter:** `avoid_init_to_null`. Use `Item? bestItem;` not `Item? bestItem = null;`

### DON'T use `true`/`false` in equality

Use `if (nonNullableBool)` not `if (nonNullableBool == true)`. For nullable: `nullableBool ?? false` or `nullableBool != null && nullableBool`.

### AVOID `late` if you need initialization checks

Use nullable instead: `String? _data; bool get isInitialized => _data != null;`

### CONSIDER null-check patterns for type promotion

```dart
if (this.response case var response?) {
  return 'Upload to ${response.url}';
}
```

Or use local variable: `final response = this.response; if (response != null) { }`

## Strings

### DO use adjacent strings for concatenation

**Linter:** `prefer_adjacent_string_concatenation`. Place literals next to each other: `'Part 1 ' 'Part 2'`.

### PREFER interpolation

**Linter:** `prefer_interpolation_to_compose_strings`. Use `'Hello, $name!'` not `'Hello, ' + name`.

### AVOID unnecessary braces in interpolation

**Linter:** `unnecessary_brace_in_string_interps`. Use `$name` not `${name}` when followed by non-alphanumeric.

## Collections

### DO use collection literals

**Linter:** `prefer_collection_literals`. Use `<Point>[]`, `<String, Address>{}`. Supports spread (`...options`) and control flow.

### DON'T use `.length` for emptiness

**Linter:** `prefer_is_empty`, `prefer_is_not_empty`. Use `isEmpty`, `isNotEmpty`.

### AVOID `forEach()` with function literal

**Linter:** `avoid_function_literals_in_foreach_calls`. Use `for (final x in list)`. Exception: `list.forEach(print)`.

### DON'T use `List.from()` unless changing type

Use `toList()` to preserve type. Use `List<int>.from()` only when converting types.

### DO use `whereType()` for type filtering

**Linter:** `prefer_iterable_whereType`. `objects.whereType<int>()`

### AVOID `cast()`

Prefer: create with right type, cast on access, or use `List.from()`. `cast()` is slow.

## Functions

### DO use function declarations

**Linter:** `prefer_function_declarations_over_variables`. Use `void localFunction() {}` not `var localFunction = () {}`.

### DON'T create lambdas when tear-offs work

**Linter:** `unnecessary_lambdas`. Use `forEach(print)`, `map(String.fromCharCode)`, `map(StringBuffer.new)`.

## Variables

### DO follow consistent `var`/`final` rules

Option 1: `final` for non-reassigned, `var` for reassigned. Option 2: `var` for all locals, `final` for fields.

### AVOID storing calculable values

Use getters instead: `get area => pi * radius * radius` not cached field.

## Members

### DON'T unnecessarily wrap fields

**Linter:** `unnecessary_getters_setters`. Use `Object? contents;` directly.

### PREFER `final` for read-only properties

`final contents = [];` not getter.

### CONSIDER `=>` for simple members

**Linter:** `prefer_expression_function_bodies`. `get area => (right - left) * (bottom - top)`.

### DON'T use `this.` except for shadowing or redirects

**Linter:** `unnecessary_this`. Needed: `this.value = value` (shadowing), `this.black()` (redirect).

### DO initialize fields at declaration

```dart
final DateTime start = DateTime.now();
```

## Constructors

### DO use initializing formals

**Linter:** `prefer_initializing_formals`. `Point(this.x, this.y)` not `Point(double x, double y) : x = x, y = y`.

### DON'T use `late` when initializer list works

Use `: x = cos(theta) * radius` not `late` field.

### DO use `;` for empty bodies

**Linter:** `empty_constructor_bodies`. `Point(this.x, this.y);`

### DON'T use `new`

**Linter:** `unnecessary_new`. Deprecated.

### DON'T use `const` redundantly

**Linter:** `unnecessary_const`. Implicit in constant contexts.

## Error Handling

### AVOID catches without `on`

**Linter:** `avoid_catches_without_on_clauses`. Filter types to avoid hiding bugs.

### DON'T discard errors

Log, display, or rethrow them.

### DO throw `Error` only for bugs

Use `ArgumentError` for bugs, `Exception` for runtime failures.

### DON'T catch `Error`

**Linter:** `avoid_catching_errors`. Fix the code instead.

### DO use `rethrow`

**Linter:** `use_rethrow_when_possible`. Use `rethrow` not `throw e` to preserve stack trace.

## Asynchrony

### PREFER async/await over raw futures

```dart
Future<int> countActive(String team) async {
  try {
    var t = await downloadTeam(team);
    if (t == null) return 0;
    return (await t.roster).where((p) => p.isActive).length;
  } catch (e) {
    return 0;
  }
}
```

### DON'T use `async` without effect

Use `async` when: using `await`, returning error async, or wrapping value in future.

### CONSIDER stream higher-order methods

Use `map()`, `where()` - they handle errors and closing correctly.

### AVOID `Completer` directly

Use `async`/`await`. Only use `Completer` for new async primitives.

### DO test `Future<T>` for `FutureOr<T>` disambiguation

```dart
if (value is Future<T>) {
  return await value;
} else {
  return value;
}
```

## Summary

These usage guidelines help you write maintainable, idiomatic Dart code:

- Use language features as intended (literals, interpolation, async/await)
- Leverage type system (null safety, generics) for safety
- Prefer simplicity over premature optimization
- Be explicit about null handling and error types
- Use modern Dart features (tear-offs, collection literals, patterns)

## Reference

Based on [Effective Dart: Usage](https://dart.dev/effective-dart/usage)
