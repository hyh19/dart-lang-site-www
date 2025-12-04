---
description: Comprehensive guidelines for designing consistent, usable Dart APIs covering naming, types, classes, members, parameters, and equality. Use when creating libraries, designing APIs, or architecting Dart applications.
alwaysApply: false
---

# Effective Dart: Design

## When to Use

Apply these rules when:

- Designing public APIs for Dart libraries
- Creating new classes, functions, or type definitions
- Naming program elements (variables, functions, classes, types)
- Deciding between different API design patterns
- Defining type annotations and managing type inference
- Implementing custom equality behavior
- Reviewing code for API consistency and usability

## Quick Reference

| Category | Key Guidelines |
|----------|----------------|
| **Naming** | Use consistent terminology; avoid abbreviations; put descriptive noun last |
| **Boolean names** | Use non-imperative verb phrases (isEnabled, hasElements, canClose) |
| **Methods (side effects)** | Use imperative verbs (add, remove, update) |
| **Methods (returns)** | Use noun phrases (elementAt, firstWhere) |
| **Conversion methods** | Use `to___()` for copies, `as___()` for views |
| **Type parameters** | Follow conventions: E (element), K/V (key/value), R (return), T/S/U (generic) |
| **Libraries** | Prefer private declarations; consider multiple classes per library |
| **Classes** | Use class modifiers (final, interface, sealed) to control extension |
| **Constructors** | Consider making constructors const when possible |
| **Fields** | Prefer making fields and top-level variables final |
| **Getters** | Use for field-like operations (no args, idempotent, no side effects) |
| **Type inference** | Annotate uninitialized variables and non-obvious fields |
| **Function types** | Annotate return types and parameters on declarations |
| **Dynamic** | Avoid unless you need dynamic dispatch; prefer Object? |
| **Parameters** | Avoid positional booleans; use named parameters |
| **Equality** | Override hashCode with ==; follow reflexive/symmetric/transitive rules |

## Names

### DO use terms consistently

Use the same name for the same thing. Good: `pageCount`, `updatePageCount()`, `toSomething()`. Bad: `renumberPages()`, `convertToSomething()`.

### AVOID abbreviations

Good: `pageCount`, `buildRectangles`, `IOStream`. Bad: `numPages`, `buildRects`.

### PREFER putting the most descriptive noun last

Good: `pageCount`, `ConversionSink`, `ChunkedConversionSink`. Bad: `numPages`, `CanvasRenderingContext2D`.

### PREFER a noun phrase for non-boolean properties

Good: `list.length`, `context.lineWidth`.

### PREFER non-imperative verb phrases for boolean properties

Start with: "to be" forms (`isEnabled`, `wasShown`), auxiliary verbs (`hasElements`, `canClose`), or active verbs (`ignoresInput`).

Good: `isEmpty`, `hasElements`, `canClose`. Bad: `empty`, `withElements`, `showPopup`.

### CONSIDER omitting the verb for named boolean parameters

Good: `Isolate.spawn(entryPoint, message, paused: false)`.

### PREFER the "positive" name for booleans

Good: `if (socket.isConnected && database.hasData)`. Bad: `if (!socket.isDisconnected && !database.isEmpty)`.

### PREFER imperative verbs for methods with side effects

Good: `list.add('element')`, `queue.removeFirst()`.

### PREFER noun phrases for methods that return values

Good: `list.elementAt(3)`, `list.firstWhere(test)`.

### AVOID starting method names with `get`

Use a getter instead, or use precise verbs (create, download, fetch, calculate).

### PREFER `to___()` for copies, `as___()` for views

Good: `list.toSet()`, `table.asMap()`.

### AVOID describing parameters in the function name

Good: `list.add(element)`. Exception: `map.containsKey(key)` disambiguates.

### DO follow type parameter conventions

`E` (element), `K`/`V` (key/value), `R` (return), `T`/`S`/`U` (generic).

## Libraries

### PREFER making declarations private

Add `_` to keep declarations private. The analyzer warns about unused private declarations.

### CONSIDER declaring multiple classes in the same library

Multiple classes per library enable "friend" classes with shared private member access.

## Classes and Mixins

### AVOID one-member abstract classes when a function will do

Use `typedef Predicate<E> = bool Function(E element);` instead of an abstract class.

### AVOID classes with only static members

Use top-level functions and variables. Exception: Grouping constants (`class Color { static const red = '#f00'; }`).

### AVOID extending/implementing classes not intended for it

Check documentation before extending or implementing.

### DO use class modifiers to control extension and implementation

`final class A {}` (no extend/implement outside library), `interface class B {}`, `base class C {}`.

### PREFER pure `mixin` or pure `class` over `mixin class`

`mixin class` is for migration; new code should use pure declarations.

## Constructors

### CONSIDER making your constructor `const` if the class supports it

If all fields are final and the constructor just initializes them, make it `const`. Note that a `const` constructor is a commitment in your public API.

## Members

### PREFER making fields and top-level variables `final`

Use `final` when possible. Consider `late final` for delayed initialization.

### DO use getters for field-like operations

Getters should: take no arguments, have no side effects, be idempotent. Good: `rectangle.area`, `collection.isEmpty`.

### DO use setters for state changes

Setters should: take one argument, change state, be idempotent. Good: `rectangle.width = 3`.

### DON'T define a setter without a getter

### AVOID runtime type tests to fake overloading

Define separate methods with different names instead.

### AVOID public `late final` fields without initializers

Use private field with public getter: `late final String _data; String get data => _data;`

### AVOID returning nullable `Future`, `Stream`, and collection types

Return empty container instead of `null`. Exception: if `null` means something different.

### AVOID returning `this` for fluent interfaces

Use method cascades: `StringBuffer()..write('one')..write('two')`.

## Types

### DO type annotate variables without initializers

`List<AstNode> parameters;` not `var parameters;`

### DO type annotate fields if type isn't obvious

Obvious: `const screenWidth = 640;` Not obvious: `Future<bool> install(...)`

### DON'T redundantly annotate initialized locals

Use `var desserts = <List<Ingredient>>[];` not `List<List<Ingredient>> desserts = ...`

### DO annotate return types and parameters on function declarations

```dart
String makeGreeting(String who) => 'Hello, $who!';
```

### DON'T annotate inferred function expression parameters

`people.map((person) => person.name)` not `(Person person)`

### DON'T annotate initializing formals

`Point(this.x, this.y)` not `Point(double this.x, double this.y)`

### DO write type arguments when not inferred

`var playerScores = <String, int>{};`

### DON'T write type arguments when inferred

`final Completer<String> response = Completer();`

### AVOID incomplete generic types

Complete: `List<num>` not `List`

### DO annotate with `dynamic` explicitly

`dynamic mergeJson(dynamic original, dynamic changes)`

### PREFER full function type signatures

`bool Function(String) test` not `Function test`

### DON'T specify return type for setters

Setters always return `void`.

### DON'T use legacy typedef syntax

`typedef Comparison<T> = int Function(T, T);`

### PREFER inline function types over typedefs

```dart
final bool Function(Event) _predicate;
```

### AVOID `dynamic` unless you need dynamic dispatch

Use `Object?` or `Object` with `is` checks instead.

### DO use `Future<void>` for async members with no return

Not `Future` or `Future<Null>`.

### AVOID `FutureOr<T>` as return type

Return `Future<int>` not `FutureOr<int>`. Use `FutureOr<T>` only in parameters.

## Parameters

### AVOID positional boolean parameters

Use named constructors or named parameters: `Task.oneShot()`, `ListBox(scroll: true)`. Not: `Task(true)`.

### AVOID optional positional parameters if users may want to omit earlier ones

Users should not need to pass a "hole".

### AVOID mandatory parameters that accept special "no argument" values

Make parameter optional instead: `substring(start)` not `substring(start, null)`.

### DO use inclusive start and exclusive end for ranges

`[0,1,2,3].sublist(1,3)` returns `[1,2]`.

## Equality

### DO override `hashCode` if you override `==`

Equal objects must have the same hash code.

### DO make `==` reflexive, symmetric, and transitive

`a == a` is true; `a == b` equals `b == a`; if `a == b` and `b == c`, then `a == c`.

### AVOID custom equality for mutable classes

Changing fields changes hash code, breaking hash-based collections.

### DON'T make `==` parameter nullable

`bool operator ==(Object other)`

## Reference

Based on [Effective Dart: Design](https://dart.dev/effective-dart/design)
