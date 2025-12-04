---
description: Comprehensive guidelines for designing consistent, usable Dart APIs covering naming, types, classes, members, parameters, and equality. Use when creating libraries, designing APIs, or architecting Dart applications.
---

# Dart Design Guidelines

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

Naming is critical for readable, maintainable code.

### DO use terms consistently

Use the same name for the same thing throughout your code. Follow existing conventions.

```dart
// Good
pageCount         // A field.
updatePageCount() // Consistent with pageCount.
toSomething()     // Consistent with Iterable's toList().
asSomething()     // Consistent with List's asMap().
Point             // A familiar concept.

// Bad
renumberPages()      // Confusingly different from pageCount.
convertToSomething() // Inconsistent with toX() precedent.
wrappedAsSomething() // Inconsistent with asX() precedent.
Cartesian            // Unfamiliar to most users.
```

Take advantage of what users already know from the problem domain, core libraries, and your own API.

### AVOID abbreviations

Unless the abbreviation is more common than the unabbreviated term, don't abbreviate.

```dart
// Good
pageCount
buildRectangles
IOStream
HttpRequest

// Bad
numPages    // "Num" is an abbreviation of "number (of)".
buildRects
InputOutputStream
HypertextTransferProtocolRequest
```

### PREFER putting the most descriptive noun last

The last word should be the most descriptive of what the thing is.

```dart
// Good
pageCount             // A count (of pages).
ConversionSink        // A sink for doing conversions.
ChunkedConversionSink // A ConversionSink that's chunked.
CssFontFaceRule       // A rule for font faces in CSS.

// Bad
numPages                  // Not a collection of pages.
CanvasRenderingContext2D  // Not a "2D".
RuleFontFaceCss           // Not a CSS.
```

### CONSIDER making the code read like a sentence

When in doubt, write code that uses your API and try to read it like a sentence.

```dart
// Good
// "If errors is empty..."
if (errors.isEmpty) {
  // ...
}

// "Hey, subscription, cancel!"
subscription.cancel();

// "Get the monsters where the monster has claws."
monsters.where((monster) => monster.hasClaws);

// Bad
// Telling errors to empty itself, or asking if it is?
if (errors.empty) {
  // ...
}

// Toggle what? To what?
subscription.toggle();

// Filter the monsters with claws *out* or include *only* those?
monsters.filter((monster) => monster.hasClaws);
```

Don't go too far and force your names to literally read like grammatically correct sentences.

```dart
// Bad (too verbose)
if (theCollectionOfErrors.isEmpty) {
  // ...
}

monsters.producesANewSequenceWhereEach((monster) => monster.hasClaws);
```

### PREFER a noun phrase for a non-boolean property or variable

The reader's focus is on what the property is.

```dart
// Good
list.length
context.lineWidth
quest.rampagingSwampBeast

// Bad
list.deleteItems
```

### PREFER a non-imperative verb phrase for a boolean property or variable

Boolean names are often used as conditions in control flow.

```dart
if (window.closeable) ...  // Adjective.
if (window.canClose) ...   // Verb.
```

Good names tend to start with:

- A form of "to be": `isEnabled`, `wasShown`, `willFire`
- An auxiliary verb: `hasElements`, `canClose`, `shouldConsume`, `mustSave`
- An active verb: `ignoresInput`, `wroteFile` (only when unambiguous)

Boolean names should not be imperative (don't sound like commands).

```dart
// Good
isEmpty
hasElements
canClose
closesWindow
canShowPopup
hasShownPopup

// Bad
empty         // Adjective or verb?
withElements  // Sounds like it might hold elements.
closeable     // Sounds like an interface.
closingWindow // Returns a bool or a window?
showPopup     // Sounds like it shows the popup.
```

### CONSIDER omitting the verb for a named boolean parameter

For named parameters that are boolean, the name is often clearer without the verb.

```dart
// Good
Isolate.spawn(entryPoint, message, paused: false);
var copy = List.from(elements, growable: true);
var regExp = RegExp(pattern, caseSensitive: false);
```

### PREFER the "positive" name for a boolean property or variable

Most boolean names have "positive" and "negative" forms. Choose the positive one.

```dart
// Good
if (socket.isConnected && database.hasData) {
  socket.write(database.read());
}

// Bad
if (!socket.isDisconnected && !database.isEmpty) {
  socket.write(database.read());
}
```

**Exception:** When the negative form is what users overwhelmingly need, use that instead.

### PREFER an imperative verb phrase for a function or method whose main purpose is a side effect

Callable members that primarily perform side effects should use imperative verb phrases.

```dart
// Good
list.add('element');
queue.removeFirst();
window.refresh();
```

### PREFER a noun phrase or non-imperative verb phrase for a function or method if returning a value is its primary purpose

Methods that primarily return values should use noun phrases.

```dart
// Good
var element = list.elementAt(3);
var first = list.firstWhere(test);
var char = string.codeUnitAt(4);
```

This guideline is deliberately softer. Sometimes methods with no side effects are still simpler to name with verbs like `list.take()` or `string.split()`.

### CONSIDER an imperative verb phrase for a function or method if you want to draw attention to the work it performs

When work is important (prone to failures, heavyweight resources), use a verb phrase.

```dart
// Good
var table = database.downloadData();
var packageVersions = packageGraph.solveConstraints();
```

Most of the time, name members based on what they do for the caller, not how they do it.

### AVOID starting a method name with `get`

In most cases, use a getter instead. If you need a method because it takes arguments:

- Drop `get` and use a noun phrase if the caller mostly cares about the value
- Use a more precise verb than `get` (create, download, fetch, calculate, request, aggregate)

### PREFER naming a method `to___()` if it copies the object's state to a new object

Conversion methods return a new object with a copy of the receiver's state.

```dart
// Good
list.toSet();
stackTrace.toString();
dateTime.toLocal();
```

### PREFER naming a method `as___()` if it returns a different representation backed by the original object

Views provide a new object that refers back to the original.

```dart
// Good
var map = table.asMap();
var list = bytes.asFloat32List();
var future = subscription.asFuture();
```

### AVOID describing the parameters in the function's or method's name

The user sees the argument at the call site, so it usually doesn't help readability.

```dart
// Good
list.add(element);
map.remove(key);

// Bad
list.addElement(element)
map.removeKey(key)
```

**Exception:** It can be useful to disambiguate from similarly-named methods:

```dart
// Good
map.containsKey(key);
map.containsValue(value);
```

### DO follow existing mnemonic conventions when naming type parameters

Single letter type parameter conventions:

- `E` for the **element** type in a collection:

```dart
// Good
class IterableBase<E> {}
class List<E> {}
class HashSet<E> {}
class RedBlackTree<E> {}
```

- `K` and `V` for the **key** and **value** types in associative collections:

```dart
// Good
class Map<K, V> {}
class Multimap<K, V> {}
class MapEntry<K, V> {}
```

- `R` for a type used as the **return** type:

```dart
// Good
abstract class ExpressionVisitor<R> {
  R visitBinary(BinaryExpression node);
  R visitLiteral(LiteralExpression node);
  R visitUnary(UnaryExpression node);
}
```

- Otherwise, use `T`, `S`, and `U` for generics with single type parameters:

```dart
// Good
class Future<T> {
  Future<S> then<S>(FutureOr<S> onValue(T value)) => ...
}
```

If none of these fit, use a descriptive name:

```dart
// Good
class Graph<N, E> {
  final List<N> nodes = [];
  final List<E> edges = [];
}

class Graph<Node, Edge> {
  final List<Node> nodes = [];
  final List<Edge> edges = [];
}
```

## Libraries

A leading underscore (`_`) indicates a member is private to its library.

### PREFER making declarations private

A public declaration is a signal that other libraries can and should access it. If that's not your intent, add the `_`. Narrow public interfaces are easier to maintain and easier for users to learn.

The analyzer will tell you about unused private declarations so you can delete dead code.

### CONSIDER declaring multiple classes in the same library

Java ties file organization to classes. Dart does not. It's fine for a single library to contain multiple classes if they logically belong together.

This enables "friend" classes since privacy works at the library level. Every class in the same library can access each other's private members.

Of course, don't put all classes into a huge monolithic library.

## Classes and Mixins

Dart is "pure" object-oriented but doesn't require all code inside classes.

### AVOID defining a one-member abstract class when a simple function will do

Dart has first-class functions. If you just need a callback, use a function.

```dart
// Good
typedef Predicate<E> = bool Function(E element);

// Bad
abstract class Predicate<E> {
  bool test(E element);
}
```

### AVOID defining a class that contains only static members

Dart has top-level functions, variables, and constants. If a function or variable isn't logically tied to a class, put it at the top level.

```dart
// Good
DateTime mostRecent(List<DateTime> dates) {
  return dates.reduce((a, b) => a.isAfter(b) ? a : b);
}

const _favoriteMammal = 'weasel';

// Bad
class DateUtils {
  static DateTime mostRecent(List<DateTime> dates) {
    return dates.reduce((a, b) => a.isAfter(b) ? a : b);
  }
}

class _Favorites {
  static const mammal = 'weasel';
}
```

In idiomatic Dart, classes define kinds of objects. A type that is never instantiated is a code smell.

**Exception:** With constants and enum-like types, grouping in a class may be natural:

```dart
// Good
class Color {
  static const red = '#f00';
  static const green = '#0f0';
  static const blue = '#00f';
  static const black = '#000';
  static const white = '#fff';
}
```

### AVOID extending a class that isn't intended to be subclassed

If a constructor changes from generative to factory, subclass constructors break. If a class changes which methods it invokes on `this`, overriding subclasses may break.

A class needs to be deliberate about allowing subclassing. Assume you should not extend a class unless documented otherwise.

### DO use class modifiers to control if your class can be extended

Use `final`, `interface`, or `sealed` to restrict extension. For example:

```dart
final class A {}        // Cannot be extended outside library
interface class B {}    // Cannot be extended outside library
```

Use these modifiers to communicate intent rather than relying on documentation.

### AVOID implementing a class that isn't intended to be an interface

Implementing a class's interface is tight coupling. Virtually any change to the class breaks your implementation.

Library maintainers need to evolve classes without breaking users. Avoid implementing implicit interfaces except for classes clearly intended to be implemented.

### DO use class modifiers to control if your class can be an interface

Use `final`, `base`, or `sealed` to prevent implementation:

```dart
final class C {}   // Cannot be implemented outside library
base class D {}    // Cannot be implemented outside library
```

While ideal, not all libraries use these modifiers. Be mindful of unintended implementation issues.

### PREFER defining a pure `mixin` or pure `class` to a `mixin class`

Dart 3.0.0 requires types intended as mixins to be declared with `mixin` or `mixin class`.

Types that need to be both mixin and class should be rare. The `mixin class` declaration is mostly for migrating pre-3.0.0 code. New code should use pure `mixin` or pure `class` declarations.

```dart
// Good
mixin Draggable {
  void drag(Offset offset) { ... }
}

class Widget {
  // ...
}

// Avoid for new code
mixin class DraggableWidget {
  void drag(Offset offset) { ... }
}
```

## Constructors

Dart constructors are created by declaring a function with the same name as the class.

### CONSIDER making your constructor `const` if the class supports it

If all fields are final and the constructor just initializes them, make it `const`. This lets users create instances in constant contexts.

Note that a `const` constructor is a commitment in your public API. If you later change to non-`const`, it breaks users calling it in constant expressions.

`const` constructors are most useful for simple, immutable value-like types.

## Members

A member belongs to an object and can be methods or instance variables.

### PREFER making fields and top-level variables `final`

State that doesn't change over time is easier to reason about. Make fields and variables `final` when you can.

Sometimes a field can't be initialized until after construction. Consider making it `late final`.

```dart
// Good
class Configuration {
  final String apiKey;
  final int timeout;
  
  Configuration(this.apiKey, this.timeout);
}
```

### DO use getters for operations that conceptually access properties

Choosing between getter and method is important for good API design.

A getter should be "field-like", meaning:

- **The operation takes no arguments and returns a result**

- **The caller cares mostly about the result.** If you want the caller to worry about how the operation produces its result, make it a method with a verb name.

  This doesn't mean the operation must be fast. `IterableBase.length` is O(n). But if it does surprising work, consider making it a method.

```dart
// Bad (surprising work for a getter)
connection.nextIncomingMessage; // Does network I/O.
expression.normalForm; // Could be exponential to calculate.
```

- **The operation has no user-visible side effects.** Accessing a field doesn't alter the object. A getter shouldn't either.

  Hidden state modifications are fine (lazy calculation, caching, logging) as long as the caller doesn't care.

```dart
// Bad (visible side effects)
stdout.newline; // Produces output.
list.clear; // Modifies object.
```

- **The operation is idempotent.** Calling it multiple times produces the same result (unless state explicitly changes).

  "Same result" doesn't mean identical objects, but the same value in aspects the caller cares about.

```dart
// Bad (not idempotent)
DateTime.now; // New result each time.
```

- **The resulting object doesn't expose all of the original object's state.** A field exposes only a piece. If your operation returns the entire state, it's likely better as a `to___()` or `as___()` method.

If all of the above describe your operation, it should be a getter.

```dart
// Good
rectangle.area;
collection.isEmpty;
button.canShow;
dataSet.minimumValue;
```

### DO use setters for operations that conceptually change properties

A setter should be "field-like", meaning:

- **The operation takes a single argument and produces no result**
- **The operation changes some state in the object**
- **The operation is idempotent.** Calling the same setter twice with the same value does nothing the second time.

```dart
// Good
rectangle.width = 3;
button.visible = false;
```

### DON'T define a setter without a corresponding getter

A "dropbox" property that can be written but not seen is confusing.

This doesn't mean you should add a getter just to permit a setter. If you have state that can be modified but not exposed, use a method instead.

```dart
// Good
class Configuration {
  int _timeout = 30;
  
  int get timeout => _timeout;
  set timeout(int value) => _timeout = value;
}
```

### AVOID using runtime type tests to fake overloading

Dart doesn't have overloading. You can fake it with `is` type tests, but this turns compile-time selection into runtime choice.

If callers usually know which type they have, define separate methods with different names. This gives better static checking and performance.

If users might have an unknown type and want the API to use `is` internally, a single method with a supertype parameter might be reasonable.

### AVOID public `late final` fields without initializers

A public `late final` field without initializer defines a public setter. This is rarely what you want.

Unless you do want users to call the setter:

- Don't use `late`
- Use a factory constructor
- Use `late`, but initialize at declaration
- Use `late`, but make it private with a public getter

```dart
// Good
class DataLoader {
  late final String _data;
  String get data => _data;
  
  Future<void> initialize() async {
    _data = await loadData();
  }
}

// Bad (public setter)
class DataLoader {
  late final String data;
  
  Future<void> initialize() async {
    data = await loadData();
  }
}
```

### AVOID returning nullable `Future`, `Stream`, and collection types

To indicate "no data", prefer returning an empty container over `null`. Users assume and prefer empty containers.

```dart
// Good
Future<List<Result>> getResults() async {
  if (noData) return [];
  // ...
}

// Avoid
Future<List<Result>?> getResults() async {
  if (noData) return null;
  // ...
}
```

**Exception:** If `null` means something different from an empty container.

### AVOID returning `this` from methods just to enable a fluent interface

Method cascades are a better solution.

```dart
// Good
var buffer =
    StringBuffer()
      ..write('one')
      ..write('two')
      ..write('three');

// Bad
var buffer =
    StringBuffer()
        .write('one')
        .write('two')
        .write('three');
```

## Types

Types can appear in two places: type annotations on declarations and type arguments to generic invocations.

### Type inference

Type annotations are optional in Dart. If you omit one, Dart tries to infer the type. Sometimes it fills in missing parts with `dynamic`, which disables type checking.

The guidelines strike a balance between brevity and control:

- Do annotate when inference doesn't have enough context
- Don't annotate locals and generic invocations unless needed
- Prefer annotating top-level variables and fields unless the initializer is obvious

### DO type annotate variables without initializers

If there's no initializer, inference fails.

```dart
// Good
List<AstNode> parameters;
if (node is Constructor) {
  parameters = node.signature;
} else if (node is Method) {
  parameters = node.parameters;
}

// Bad
var parameters;
if (node is Constructor) {
  parameters = node.signature;
} else if (node is Method) {
  parameters = node.parameters;
}
```

### DO type annotate fields and top-level variables if the type isn't obvious

Type annotations document how a library should be used. They form boundaries to isolate type errors.

```dart
// Bad (unclear)
install(id, destination) => ...

// Good (clear)
Future<bool> install(PackageId id, String destination) => ...
```

When the type is obvious from the initializer, you may omit it:

```dart
// Good
const screenWidth = 640; // Inferred as int.
```

"Obvious" candidates:

- Literals
- Constructor invocations
- References to explicitly typed constants
- Simple expressions on numbers and strings
- Factory methods like `int.parse()`, `Future.wait()`

When in doubt, add a type annotation. Even when obvious, you may want to annotate if the inferred type relies on other libraries.

### DON'T redundantly type annotate initialized local variables

Local variables have little scope. Omitting the type focuses on the name and value.

```dart
// Good
List<List<Ingredient>> possibleDesserts(Set<Ingredient> pantry) {
  var desserts = <List<Ingredient>>[];
  for (final recipe in cookbook) {
    if (pantry.containsAll(recipe)) {
      desserts.add(recipe);
    }
  }
  return desserts;
}

// Bad
List<List<Ingredient>> possibleDesserts(Set<Ingredient> pantry) {
  List<List<Ingredient>> desserts = <List<Ingredient>>[];
  for (final List<Ingredient> recipe in cookbook) {
    if (pantry.containsAll(recipe)) {
      desserts.add(recipe);
    }
  }
  return desserts;
}
```

Sometimes the inferred type isn't what you want. Annotate when needed:

```dart
// Good
Widget build(BuildContext context) {
  Widget result = Text('You won!');
  if (applyPadding) {
    result = Padding(padding: EdgeInsets.all(8.0), child: result);
  }
  return result;
}
```

### DO annotate return types on function declarations

Dart doesn't infer return types from function bodies. Write them explicitly.

```dart
// Good
String makeGreeting(String who) {
  return 'Hello, $who!';
}

// Bad
makeGreeting(String who) {
  return 'Hello, $who!';
}
```

This applies to non-local functions (top-level, static, instance methods). Local functions and anonymous functions infer return types.

### DO annotate parameter types on function declarations

A function's parameter list is its boundary. Annotating parameter types makes that boundary well defined.

```dart
// Good
void sayRepeatedly(String message, {int count = 2}) {
  for (var i = 0; i < count; i++) {
    print(message);
  }
}

// Bad
void sayRepeatedly(message, {count = 2}) {
  for (var i = 0; i < count; i++) {
    print(message);
  }
}
```

**Exception:** Function expressions and initializing formals have different conventions.

### DON'T annotate inferred parameter types on function expressions

Anonymous functions are usually passed to methods expecting callbacks. Dart infers parameter types from context.

```dart
// Good
var names = people.map((person) => person.name);

// Bad
var names = people.map((Person person) => person.name);
```

If the context isn't precise enough, you may need to annotate. If the function isn't used immediately, consider making it a named declaration.

### DON'T type annotate initializing formals

If a constructor parameter uses `this.` to initialize a field, or `super.` to forward a super parameter, the type is inferred.

```dart
// Good
class Point {
  double x, y;
  Point(this.x, this.y);
}

class MyWidget extends StatelessWidget {
  MyWidget({super.key});
}

// Bad
class Point {
  double x, y;
  Point(double this.x, double this.y);
}

class MyWidget extends StatelessWidget {
  MyWidget({Key? super.key});
}
```

### DO write type arguments on generic invocations that aren't inferred

Dart infers type arguments from expected types and passed values. When that's not enough, write the type argument list explicitly.

```dart
// Good
var playerScores = <String, int>{};
final events = StreamController<Event>();

// Bad
var playerScores = {};
final events = StreamController();
```

If the invocation is a variable initializer, you can annotate the variable instead:

```dart
// Good
class Downloader {
  final Completer<String> response = Completer();
}

// Bad
class Downloader {
  final response = Completer();
}
```

### DON'T write type arguments on generic invocations that are inferred

If an invocation's type arguments are correctly inferred, omit them.

```dart
// Good
class Downloader {
  final Completer<String> response = Completer();
}

// Bad
class Downloader {
  final Completer<String> response = Completer<String>();
}
```

### AVOID writing incomplete generic types

If you write a generic type name without type arguments, you haven't fully specified the type. Dart fills missing arguments with `dynamic`.

```dart
// Bad
List numbers = [1, 2, 3];
var completer = Completer<Map>();

// Good
List<num> numbers = [1, 2, 3];
var completer = Completer<Map<String, int>>();
```

### DO annotate with `dynamic` instead of letting inference fail

When inference doesn't fill in a type, it defaults to `dynamic`. If that's what you want, write it explicitly to clarify intent.

```dart
// Good
dynamic mergeJson(dynamic original, dynamic changes) => ...

// Bad
mergeJson(original, changes) => ...
```

It's OK to omit the type when Dart successfully infers `dynamic`:

```dart
// Good
Map<String, dynamic> readJson() => ...

void printUsers() {
  var json = readJson();
  var users = json['users'];
  print(users);
}
```

**Exception:** Type annotations on unused parameters (`_`) can be omitted.

### PREFER signatures in function type annotations

The identifier `Function` alone is only marginally more useful than `dynamic`. Prefer full function types.

```dart
// Good
bool isValid(String value, bool Function(String) test) => ...

// Bad
bool isValid(String value, Function test) => ...
```

**Exception:** When you want a union of multiple function types:

```dart
// Good
void handleError(void Function() operation, Function errorHandler) {
  try {
    operation();
  } catch (err, stack) {
    if (errorHandler is Function(Object)) {
      errorHandler(err);
    } else if (errorHandler is Function(Object, StackTrace)) {
      errorHandler(err, stack);
    } else {
      throw ArgumentError('errorHandler has wrong signature.');
    }
  }
}
```

### DON'T specify a return type for a setter

Setters always return `void`. Writing it is pointless.

```dart
// Good
set foo(Foo value) {
   ...
}

// Bad
void set foo(Foo value) {
   ...
}
```

### DON'T use the legacy typedef syntax

The original syntax:

```dart
// Bad
typedef int Comparison<T>(T a, T b);
```

Has problems. Use the new syntax:

```dart
// Good
typedef Comparison<T> = int Function(T, T);
```

You can include parameter names:

```dart
// Good
typedef Comparison<T> = int Function(T a, T b);
```

### PREFER inline function types over typedefs

Dart supports inline function type syntax anywhere a type annotation is allowed:

```dart
// Good
class FilteredObservable {
  final bool Function(Event) _predicate;
  final List<void Function(Event)> _observers;

  FilteredObservable(this._predicate, this._observers);

  void Function(Event)? notify(Event event) {
    if (!_predicate(event)) return null;

    void Function(Event)? last;
    for (final observer in _observers) {
      observer(event);
      last = observer;
    }

    return last;
  }
}
```

It may be worth defining a typedef if the function type is particularly long or frequently used. But in most cases, users want to see the actual function type where it's used.

### PREFER using function type syntax for parameters

Dart has special syntax for function-typed parameters:

```dart
Iterable<T> where(bool predicate(T element)) => ...
```

Now that Dart has general function type notation, use it for parameters too:

```dart
// Good
Iterable<T> where(bool Function(T) predicate) => ...
```

### AVOID using `dynamic` unless you want to disable static checking

Some operations work with any object. Two types permit all values: `Object?` and `dynamic`. However:

- `Object?` accepts all objects (including null)
- `Object` accepts all objects except null
- `dynamic` accepts all objects AND permits all operations

Use `dynamic` only when you need dynamic dispatch. Otherwise, use `Object?` or `Object` with `is` checks and type promotion.

```dart
// Good
/// Returns a Boolean representation for [arg], which must
/// be a String or bool.
bool convertToBool(Object arg) {
  if (arg is bool) return arg;
  if (arg is String) return arg.toLowerCase() == 'true';
  throw ArgumentError('Cannot convert $arg to a bool.');
}
```

The main exception is when working with existing APIs using `dynamic`, like JSON (`Map<String, dynamic>`).

### DO use `Future<void>` as the return type of asynchronous members that do not produce values

The asynchronous equivalent of `void` is `Future<void>`.

Don't use `Future` or `Future<Null>`. Use `Future<void>` for better error-checking.

For asynchronous functions where no callers need to await the work, use a return type of `void`.

### AVOID using `FutureOr<T>` as a return type

If you accept `FutureOr<int>`, you're generous in what you accept. But if you return `FutureOr<int>`, users must check whether they got an `int` or `Future<int>`.

Just return `Future<int>`. It's clearer. A function should be always asynchronous or always synchronous.

```dart
// Good
Future<int> triple(FutureOr<int> value) async => (await value) * 3;

// Bad
FutureOr<int> triple(FutureOr<int> value) {
  if (value is int) return value * 3;
  return value.then((v) => v * 3);
}
```

The precise rule: only use `FutureOr<T>` in contravariant positions (parameters). It's OK for a callback's return type to be `FutureOr<T>`:

```dart
// Good
Stream<S> asyncMap<T, S>(
  Iterable<T> iterable,
  FutureOr<S> Function(T) callback,
) async* {
  for (final element in iterable) {
    yield await callback(element);
  }
}
```

## Parameters

Optional parameters can be positional or named, but not both.

### AVOID positional boolean parameters

Booleans are usually used in literal form. This can make call sites unreadable:

```dart
// Bad
new Task(true);
new Task(false);
new ListBox(false, true, true);
new Button(false);
```

Prefer named arguments, named constructors, or named constants:

```dart
// Good
Task.oneShot();
Task.repeating();
ListBox(scroll: true, showScrollbars: true);
Button(ButtonState.enabled);
```

**Note:** This doesn't apply to setters where the name makes it clear:

```dart
// Good
listBox.canScroll = true;
button.isEnabled = false;
```

### AVOID optional positional parameters if the user may want to omit earlier parameters

Optional positional parameters should have a logical progression. Users should almost never need to pass a "hole" to omit an earlier parameter.

```dart
// Good
String.fromCharCodes(Iterable<int> charCodes, [int start = 0, int? end]);

DateTime(
  int year, [
  int month = 1,
  int day = 1,
  int hour = 0,
  int minute = 0,
  int second = 0,
  int millisecond = 0,
  int microsecond = 0,
]);

Duration({
  int days = 0,
  int hours = 0,
  int minutes = 0,
  int seconds = 0,
  int milliseconds = 0,
  int microseconds = 0,
});
```

### AVOID mandatory parameters that accept a special "no argument" value

If the user is logically omitting a parameter, let them actually omit it by making it optional.

```dart
// Good
var rest = string.substring(start);

// Bad
var rest = string.substring(start, null);
```

### DO use inclusive start and exclusive end parameters to accept a range

When defining a method for selecting a range from an integer-indexed sequence, take a start index (first item) and optional end index (one greater than the last item).

```dart
// Good
[0, 1, 2, 3].sublist(1, 3) // [1, 2]
'abcd'.substring(1, 3) // 'bc'
```

This is consistent with core libraries.

## Equality

Implementing custom equality can be tricky.

### DO override `hashCode` if you override `==`

The default hash code provides identity hashing. If you override `==`, you may have different objects that are "equal". They must have the same hash code, or hash-based collections will fail.

### DO make your `==` operator obey the mathematical rules of equality

An equivalence relation should be:

- **Reflexive**: `a == a` should always return `true`
- **Symmetric**: `a == b` should return the same as `b == a`
- **Transitive**: If `a == b` and `b == c` both return `true`, then `a == c` should too

If your class can't obey these rules, `==` isn't the right name for the operation.

### AVOID defining custom equality for mutable classes

When you define `==`, you also define `hashCode`. Both should consider the object's fields. If those fields change, the hash code changes.

Most hash-based collections assume an object's hash code is constant and may behave unpredictably otherwise.

### DON'T make the parameter to `==` nullable

The language specifies that `null` is equal only to itself, and that `==` is called only if the right-hand side is not `null`.

```dart
// Good
class Person {
  final String name;

  // ···

  bool operator ==(Object other) => other is Person && name == other.name;
}

// Bad
class Person {
  final String name;

  // ···

  bool operator ==(Object? other) =>
      other != null && other is Person && name == other.name;
}
```

## Reference

Based on [Effective Dart: Design](https://dart.dev/effective-dart/design)
