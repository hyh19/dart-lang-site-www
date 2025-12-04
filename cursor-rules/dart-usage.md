---
description: Guidelines for using Dart language features to write maintainable code. Covers libraries, null safety, strings, collections, functions, variables, members, constructors, error handling, and asynchrony.
---

# Dart Usage Best Practices

## When to Use

Apply these guidelines when writing Dart code bodies and implementing functionality. These rules help you:

- Compose programs from multiple files consistently
- Handle null safety and nullable types correctly
- Work with strings, collections, and functions idiomatically
- Design classes with proper constructors and members
- Handle errors and asynchronous operations effectively

Users of your library may not notice these practices, but maintainers definitely will.

## Libraries

Guidelines for organizing code across multiple files using imports and exports.

### DO use strings in `part of` directives

**Linter rule:** `use_string_in_part_of_directives`

If you use `part` to split a library into multiple files, use URI strings (not library names) in the `part of` directive to avoid ambiguity.

**Good:**

```dart
// In some/other/file.dart
part of '../../my_library.dart';
```

**Bad:**

```dart
// In some/other/file.dart
part of my_library;
```

**Why:** Library names are a legacy feature and can introduce ambiguity when determining which library a part belongs to.

### DON'T import libraries inside the `src` directory of another package

**Linter rule:** `implementation_imports`

The `src` directory contains implementation-private code. Package maintainers can make breaking changes to `src` code without it being a breaking change to the package version.

**Why:** If you import another package's private library, a minor release of that package could break your code.

### DON'T allow an import path to reach into or out of `lib`

**Linter rule:** `avoid_relative_lib_imports`

A `package:` import lets you access a library inside a package's `lib` directory without worrying about where the package is stored. Never use relative imports that reach into or out of `lib`.

**Bad:**

```dart
// In test/api_test.dart
import '../lib/api.dart';  // DON'T reach into lib
```

**Good:**

```dart
// In test/api_test.dart
import 'package:my_package/api.dart';
```

**Why:** Mixing relative and package imports creates confusion about whether two imports refer to the same library.

### PREFER relative import paths

**Linter rule:** `prefer_relative_imports`

When an import doesn't reach across `lib`, use relative imports. They're shorter and keep related code together.

**Good:**

```dart
// In lib/src/utils.dart
import '../api.dart';
import 'stuff.dart';
```

**Bad:**

```dart
// In lib/src/utils.dart
import 'package:my_package/api.dart';
import 'package:my_package/src/stuff.dart';
```

## Null Safety

Best practices for handling nullable types and null values.

### DON'T explicitly initialize variables to `null`

**Linter rule:** `avoid_init_to_null`

Nullable variables are implicitly initialized to `null`, so explicit initialization is redundant.

**Good:**

```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem;
  
  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }
  
  return bestItem;
}
```

**Bad:**

```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem = null;  // Redundant
  // ...
}
```

### DON'T use an explicit default value of `null`

**Linter rule:** `avoid_init_to_null`

Optional nullable parameters implicitly default to `null`.

**Good:**

```dart
void error([String? message]) {
  stderr.write(message ?? '\n');
}
```

**Bad:**

```dart
void error([String? message = null]) {
  stderr.write(message ?? '\n');
}
```

### DON'T use `true` or `false` in equality operations

For non-nullable boolean expressions, comparing to `true` or `false` is redundant.

**Good:**

```dart
if (nonNullableBool) { ... }
if (!nonNullableBool) { ... }
```

**Bad:**

```dart
if (nonNullableBool == true) { ... }
if (nonNullableBool == false) { ... }
```

For nullable boolean expressions, use `??` or explicit `!= null` checks:

**Good:**

```dart
// If you want null to result in false:
if (nullableBool ?? false) { ... }

// If you want null to be false and type promotion:
if (nullableBool != null && nullableBool) { ... }
```

**Bad:**

```dart
if (nullableBool == true) { ... }  // Confusing logic
```

**Why:** The `??` operator makes null handling explicit and clear. The expression `nullableBool == true` is confusing because it's not evidently null-related.

### AVOID `late` variables if you need to check initialization

Dart provides no way to check if a `late` variable has been initialized. If you need to track initialization status, use a nullable variable instead.

**Better:**

```dart
class StateManager {
  String? _cachedData;  // Can check if initialized
  
  bool get isInitialized => _cachedData != null;
}
```

**Avoid:**

```dart
class StateManager {
  late String _cachedData;  // Cannot check initialization
  bool _initialized = false;  // Redundant tracking
}
```

**Exception:** If `null` is a valid initialized value, then a separate boolean flag makes sense.

### CONSIDER type promotion or null-check patterns for nullable types

When null-check type promotion isn't available (for non-local variables, mutable fields), use null-check patterns or local variables.

**Using null-check pattern:**

```dart
class UploadException {
  final Response? response;
  
  UploadException([this.response]);
  
  @override
  String toString() {
    if (this.response case var response?) {
      return 'Could not complete upload to ${response.url} '
          '(error code ${response.errorCode}): ${response.reason}.';
    }
    return 'Could not upload (no response).';
  }
}
```

**Using local variable:**

```dart
class UploadException {
  final Response? response;
  
  UploadException([this.response]);
  
  @override
  String toString() {
    final response = this.response;
    if (response != null) {
      return 'Could not complete upload to ${response.url} '
          '(error code ${response.errorCode}): ${response.reason}.';
    }
    return 'Could not upload (no response).';
  }
}
```

**Why:** Fields can't be type promoted because they might change. Local variables and null-check patterns solve this.

**Caution:** Be careful with local variables if you need to write back to the field, and watch for stale values if the field might change.

## Strings

Best practices for working with string literals and interpolation.

### DO use adjacent strings to concatenate string literals

**Linter rule:** `prefer_adjacent_string_concatenation`

Place string literals next to each other to concatenate them (no `+` needed).

**Good:**

```dart
raiseAlarm(
  'ERROR: Parts of the spaceship are on fire. Other '
  'parts are overrun by martians. Unclear which are which.',
);
```

**Bad:**

```dart
raiseAlarm(
  'ERROR: Parts of the spaceship are on fire. Other ' +
      'parts are overrun by martians. Unclear which are which.',
);
```

### PREFER using interpolation to compose strings and values

**Linter rule:** `prefer_interpolation_to_compose_strings`

String interpolation is cleaner and shorter than concatenation.

**Good:**

```dart
'Hello, $name! You are ${year - birth} years old.';
```

**Bad:**

```dart
'Hello, ' + name + '! You are ' + (year - birth).toString() + ' years old.';
```

**Note:** This applies to combining multiple literals and values. Using `.toString()` for a single object is fine.

### AVOID using curly braces in interpolation when not needed

**Linter rule:** `unnecessary_brace_in_string_interps`

Omit `{}` when interpolating a simple identifier not followed by alphanumeric text.

**Good:**

```dart
var greeting = 'Hi, $name! I love your ${decade}s costume.';
```

**Bad:**

```dart
var greeting = 'Hi, ${name}! I love your ${decade}s costume.';
```

## Collections

Best practices for working with lists, maps, sets, and other collections.

### DO use collection literals when possible

**Linter rule:** `prefer_collection_literals`

Use literal syntax for List, Map, and Set creation—it's more concise and gives access to spread operators and control flow.

**Good:**

```dart
var points = <Point>[];
var addresses = <String, Address>{};
var counts = <int>{};
```

**Bad:**

```dart
var addresses = Map<String, Address>();
var counts = Set<int>();
```

**Powerful features of literals:**

```dart
var arguments = [
  ...options,
  command,
  ...?modeFlags,
  for (var path in filePaths)
    if (path.endsWith('.dart')) path.replaceAll('.dart', '.js'),
];
```

### DON'T use `.length` to see if a collection is empty

**Linter rule:** `prefer_is_empty`, `prefer_is_not_empty`

Use `.isEmpty` and `.isNotEmpty` instead. They're faster (O(1) vs potentially O(n)) and more readable.

**Good:**

```dart
if (lunchBox.isEmpty) return 'so hungry...';
if (words.isNotEmpty) return words.join(' ');
```

**Bad:**

```dart
if (lunchBox.length == 0) return 'so hungry...';
if (!words.isEmpty) return words.join(' ');
```

### AVOID using `Iterable.forEach()` with a function literal

**Linter rule:** `avoid_function_literals_in_foreach_calls`

Use a `for-in` loop instead—it's more idiomatic in Dart.

**Good:**

```dart
for (final person in people) {
  ...
}
```

**Bad:**

```dart
people.forEach((person) {
  ...
});
```

**Exception:** Using `forEach()` with an existing function is fine:

```dart
people.forEach(print);
```

**Note:** `Map.forEach()` is always fine since maps aren't iterable.

### DON'T use `List.from()` unless you intend to change the type

`List.from()` loses the type argument. Use `.toList()` to preserve it.

**Good:**

```dart
var iterable = [1, 2, 3];
print(iterable.toList().runtimeType);  // List<int>
```

**Bad:**

```dart
var iterable = [1, 2, 3];
print(List.from(iterable).runtimeType);  // List<dynamic>
```

**When to use `List.from()`:** When you explicitly want to change the type:

```dart
var numbers = [1, 2.3, 4]; // List<num>
numbers.removeAt(1); // Now only contains integers
var ints = List<int>.from(numbers);
```

### DO use `whereType()` to filter a collection by type

**Linter rule:** `prefer_iterable_whereType`

`whereType()` is concise and produces an iterable with the correct type.

**Good:**

```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.whereType<int>();
```

**Bad:**

```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int);  // Returns Iterable<Object>
var ints = objects.where((e) => e is int).cast<int>();  // Verbose
```

### DON'T use `cast()` when a nearby operation will do

Instead of tacking on `cast()`, see if an existing transformation can change the type.

**Using `List<T>.from()`:**

```dart
// Good
var stuff = <dynamic>[1, 2];
var ints = List<int>.from(stuff);

// Bad
var stuff = <dynamic>[1, 2];
var ints = stuff.toList().cast<int>();
```

**Using explicit type argument with `map()`:**

```dart
// Good
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map<double>((n) => n * 2);

// Bad
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map((n) => n * 2).cast<double>();
```

### AVOID using `cast()`

Prefer alternatives to `cast()` when possible:

1. **Create it with the right type** - Fix the type at creation
2. **Cast elements on access** - Cast during iteration if you only access a few elements
3. **Eagerly cast using `List.from()`** - Convert once if you'll access most elements

**Create with right type:**

```dart
// Good
List<int> singletonList(int value) {
  var list = <int>[];
  list.add(value);
  return list;
}

// Bad
List<int> singletonList(int value) {
  var list = [];
  list.add(value);
  return list.cast<int>();
}
```

**Cast on access:**

```dart
// Good
void printEvens(List<Object> objects) {
  for (final n in objects) {
    if ((n as int).isEven) print(n);
  }
}

// Bad
void printEvens(List<Object> objects) {
  for (final n in objects.cast<int>()) {
    if (n.isEven) print(n);
  }
}
```

**Why:** `cast()` returns a lazy wrapper that checks types on every operation, which can be slow and may fail at runtime.

## Functions

Best practices for defining and using functions.

### DO use a function declaration to bind a function to a name

**Linter rule:** `prefer_function_declarations_over_variables`

Use function declaration syntax instead of assigning a lambda to a variable.

**Good:**

```dart
void main() {
  void localFunction() {
    ...
  }
}
```

**Bad:**

```dart
void main() {
  var localFunction = () {
    ...
  };
}
```

### DON'T create a lambda when a tear-off will do

**Linter rule:** `unnecessary_lambdas`

When you need a closure that invokes a named function with the same parameters, use a tear-off instead of wrapping it in a lambda.

**Good:**

```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach(print);

// Method:
charCodes.forEach(buffer.write);

// Named constructor:
var strings = charCodes.map(String.fromCharCode);

// Unnamed constructor:
var buffers = charCodes.map(StringBuffer.new);
```

**Bad:**

```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach((code) {
  print(code);
});

// Method:
charCodes.forEach((code) {
  buffer.write(code);
});

// Named constructor:
var strings = charCodes.map((code) => String.fromCharCode(code));

// Unnamed constructor:
var buffers = charCodes.map((code) => StringBuffer(code));
```

## Variables

Best practices for declaring and using variables.

### DO follow a consistent rule for `var` and `final` on local variables

Choose one of these approaches and apply it consistently:

**Option 1:** Use `final` for non-reassigned variables, `var` for reassigned ones.

**Option 2:** Use `var` for all local variables (but still use `final` for fields and top-level variables).

**Why:** Consistency helps readers understand intent. When they see `var`, they know what to expect based on your project's convention.

### AVOID storing what you can calculate

Don't cache calculated values unless you have a proven performance problem. Caches add complexity and can cause bugs when invalidation isn't handled correctly.

**Good:**

```dart
class Circle {
  double radius;
  
  Circle(this.radius);
  
  double get area => pi * radius * radius;
  double get circumference => pi * 2.0 * radius;
}
```

**Bad:**

```dart
class Circle {
  double radius;
  double area;
  double circumference;
  
  Circle(double radius)
    : radius = radius,
      area = pi * radius * radius,
      circumference = pi * 2.0 * radius;
}
```

**Why:** The bad example wastes memory and the cache becomes stale when `radius` is reassigned.

## Members

Best practices for class members (methods and fields).

### DON'T wrap a field in a getter and setter unnecessarily

**Linter rule:** `unnecessary_getters_setters`

In Dart, fields and getters/setters are indistinguishable to callers. You can expose a field directly and wrap it later if needed.

**Good:**

```dart
class Box {
  Object? contents;
}
```

**Bad:**

```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
  set contents(Object? value) {
    _contents = value;
  }
}
```

### PREFER using a `final` field to make a read-only property

If external code should read but not assign a field, use `final`.

**Good:**

```dart
class Box {
  final contents = [];
}
```

**Bad:**

```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
}
```

**Note:** If you need to internally assign outside the constructor, then use the private field + public getter pattern.

### CONSIDER using `=>` for simple members

**Linter rule:** `prefer_expression_function_bodies`

Use `=>` for members that calculate and return a value in a single expression.

**Good:**

```dart
double get area => (right - left) * (bottom - top);

String capitalize(String name) =>
    '${name[0].toUpperCase()}${name.substring(1)}';
```

**When not to use:** Avoid `=>` for complex expressions with deep nesting (cascades, nested conditionals). Use a block body instead.

**Good:**

```dart
Treasure? openChest(Chest chest, Point where) {
  if (_opened.containsKey(chest)) return null;
  
  var treasure = Treasure(where);
  treasure.addAll(chest.contents);
  _opened[chest] = treasure;
  return treasure;
}
```

**Bad:**

```dart
Treasure? openChest(Chest chest, Point where) => _opened.containsKey(chest)
    ? null
    : _opened[chest] = (Treasure(where)..addAll(chest.contents));
```

**Setters with `=>`:**

```dart
num get x => center.x;
set x(num value) => center = Point(value, center.y);
```

### DON'T use `this.` except to redirect to a named constructor or avoid shadowing

**Linter rule:** `unnecessary_this`

Dart doesn't require `this.` to access members. Only use it when necessary:

**Use case 1: Avoiding shadowing:**

```dart
class Box {
  Object? value;
  
  void update(Object? value) {
    this.value = value;  // Necessary to distinguish from parameter
  }
}
```

**Use case 2: Redirecting to named constructor:**

```dart
class ShadeOfGray {
  final int brightness;
  
  ShadeOfGray(int val) : brightness = val;
  ShadeOfGray.black() : this(0);
  ShadeOfGray.alsoBlack() : this.black();  // Necessary
}
```

**Unnecessary usage:**

```dart
// Bad
class Box {
  Object? value;
  
  void clear() {
    this.update(null);  // Unnecessary
  }
}

// Good
class Box {
  Object? value;
  
  void clear() {
    update(null);
  }
}
```

### DO initialize fields at their declaration when possible

If a field doesn't depend on constructor parameters, initialize it at declaration. This reduces code duplication across multiple constructors.

**Good:**

```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();
  
  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

**Bad:**

```dart
class ProfileMark {
  final String name;
  final DateTime start;
  
  ProfileMark(this.name) : start = DateTime.now();
  ProfileMark.unnamed() : name = '', start = DateTime.now();
}
```

**Note:** `late` fields can reference `this` in their initializers, enabling more complex initialization at declaration.

## Constructors

Best practices for defining constructors.

### DO use initializing formals when possible

**Linter rule:** `prefer_initializing_formals`

Use `this.` syntax in constructor parameters to initialize fields directly.

**Good:**

```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

**Bad:**

```dart
class Point {
  double x, y;
  Point(double x, double y) : x = x, y = y;
}
```

### DON'T use `late` when a constructor initializer list will do

Prefer initializer lists over `late` fields for constructor-time initialization.

**Good:**

```dart
class Point {
  double x, y;
  Point.polar(double theta, double radius)
    : x = cos(theta) * radius,
      y = sin(theta) * radius;
}
```

**Bad:**

```dart
class Point {
  late double x, y;
  Point.polar(double theta, double radius) {
    x = cos(theta) * radius;
    y = sin(theta) * radius;
  }
}
```

**Why:** Initializer lists provide static safety and better performance compared to `late` fields.

### DO use `;` instead of `{}` for empty constructor bodies

**Linter rule:** `empty_constructor_bodies`

Terminate constructors with empty bodies using a semicolon.

**Good:**

```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

**Bad:**

```dart
class Point {
  double x, y;
  Point(this.x, this.y) {}
}
```

### DON'T use `new`

**Linter rule:** `unnecessary_new`

The `new` keyword is optional and deprecated. Don't use it.

**Good:**

```dart
Widget build(BuildContext context) {
  return Row(
    children: [
      RaisedButton(child: Text('Increment')),
      Text('Click!'),
    ],
  );
}
```

**Bad:**

```dart
Widget build(BuildContext context) {
  return new Row(
    children: [
      new RaisedButton(child: new Text('Increment')),
      new Text('Click!'),
    ],
  );
}
```

### DON'T use `const` redundantly

**Linter rule:** `unnecessary_const`

In contexts where expressions must be constant, `const` is implicit. Omit it in:

- Const collection literals
- Const constructor calls
- Metadata annotations
- Const variable initializers
- Switch case expressions

**Good:**

```dart
const primaryColors = [
  Color('red', [255, 0, 0]),
  Color('green', [0, 255, 0]),
  Color('blue', [0, 0, 255]),
];
```

**Bad:**

```dart
const primaryColors = const [
  const Color('red', const [255, 0, 0]),
  const Color('green', const [0, 255, 0]),
  const Color('blue', const [0, 0, 255]),
];
```

## Error Handling

Best practices for catching and throwing exceptions.

### AVOID catches without `on` clauses

**Linter rule:** `avoid_catches_without_on_clauses`

Catching everything is rarely what you want. Filter the types you catch with `on` clauses.

**Why:** Unfiltered catches can hide programming errors like `StackOverflowError`, `OutOfMemoryError`, `ArgumentError`, and failed `assert()` statements.

**When necessary:** If you must catch everything, catch `Exception` rather than all types—it excludes programmatic errors.

### DON'T discard errors from catches without `on` clauses

If you catch everything, do something with it—log it, display it, or rethrow it. Don't silently discard exceptions.

### DO throw objects that implement `Error` only for programmatic errors

`Error` and its subtypes (like `ArgumentError`) indicate bugs in your code. Throw these when the API is being used incorrectly.

For runtime failures that aren't code bugs, throw `Exception` or another type instead.

### DON'T explicitly catch `Error` or types that implement it

**Linter rule:** `avoid_catching_errors`

Errors indicate bugs and should unwind the callstack, halt the program, and print a stack trace. Catching them masks bugs.

**Fix the cause:** Instead of catching errors, fix the code causing them to be thrown.

### DO use `rethrow` to rethrow a caught exception

**Linter rule:** `use_rethrow_when_possible`

Use `rethrow` instead of `throw e` to preserve the original stack trace.

**Good:**

```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) rethrow;
  handle(e);
}
```

**Bad:**

```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) throw e;  // Resets stack trace
  handle(e);
}
```

## Asynchrony

Best practices for asynchronous programming with futures and streams.

### PREFER async/await over using raw futures

`async`/`await` makes asynchronous code more readable and lets you use all Dart control flow structures.

**Good:**

```dart
Future<int> countActivePlayers(String teamName) async {
  try {
    var team = await downloadTeam(teamName);
    if (team == null) return 0;
    
    var players = await team.roster;
    return players.where((player) => player.isActive).length;
  } on DownloadException catch (e, _) {
    log.error(e);
    return 0;
  }
}
```

**Bad:**

```dart
Future<int> countActivePlayers(String teamName) {
  return downloadTeam(teamName)
      .then((team) {
        if (team == null) return Future.value(0);
        
        return team.roster.then((players) {
          return players.where((player) => player.isActive).length;
        });
      })
      .onError<DownloadException>((e, _) {
        log.error(e);
        return 0;
      });
}
```

### DON'T use `async` when it has no useful effect

If you can omit `async` without changing behavior, do so.

**Good:**

```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) {
  return Future.any([left, right]);
}
```

**Bad:**

```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) async {
  return Future.any([left, right]);
}
```

**When `async` is useful:**

1. You're using `await`
2. You're returning an error asynchronously (`async` + `throw` is shorter than `return Future.error(...)`)
3. You're returning a value and want it wrapped in a future (`async` is shorter than `Future.value(...)`)

**Good uses:**

```dart
Future<void> usesAwait(Future<String> later) async {
  print(await later);
}

Future<void> asyncError() async {
  throw 'Error!';
}

Future<String> asyncValue() async => 'value';
```

### CONSIDER using higher-order methods to transform a stream

Use stream methods like `map()`, `where()`, etc. They handle errors, closing, and other stream details correctly.

### AVOID using Completer directly

Most code should use `async`/`await` or `Future.then()` instead of `Completer`.

**Bad:**

```dart
Future<bool> fileContainsBear(String path) {
  var completer = Completer<bool>();
  
  File(path).readAsString().then((contents) {
    completer.complete(contents.contains('bear'));
  });
  
  return completer.future;
}
```

**Good with `then()`:**

```dart
Future<bool> fileContainsBear(String path) {
  return File(path).readAsString().then((contents) {
    return contents.contains('bear');
  });
}
```

**Good with `async`/`await`:**

```dart
Future<bool> fileContainsBear(String path) async {
  var contents = await File(path).readAsString();
  return contents.contains('bear');
}
```

**When to use `Completer`:** Only for implementing new asynchronous primitives or interfacing with async code that doesn't use futures.

### DO test for `Future<T>` when disambiguating a `FutureOr<T>` whose type argument could be `Object`

When working with `FutureOr<T>` where `T` could be `Object`, test for the `Future` case explicitly.

**Good:**

```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is Future<T>) {
    var result = await value;
    print(result);
    return result;
  } else {
    print(value);
    return value;
  }
}
```

**Bad:**

```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is T) {
    print(value);
    return value;
  } else {
    var result = await value;
    print(result);
    return result;
  }
}
```

**Why:** `Future<Object>` implements `Object`, so `is T` returns true even when the value is a future, causing incorrect behavior.

## Summary

These usage guidelines help you write maintainable, idiomatic Dart code. Key principles:

- Use language features as intended (literals, interpolation, async/await)
- Leverage type system (null safety, generics) for safety
- Prefer simplicity over premature optimization (avoid caching, use calculated properties)
- Be explicit about null handling and error types
- Use modern Dart features (tear-offs, collection literals, patterns)

For more detailed guidance, see:

- [Effective Dart: Style](/effective-dart/style) - Naming and formatting
- [Effective Dart: Documentation](/effective-dart/documentation) - Doc comments
- [Effective Dart: Design](/effective-dart/design) - API design
