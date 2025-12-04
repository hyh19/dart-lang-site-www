---
description: Guide for navigating and applying the four Effective Dart rules (Style, Design, Usage, Documentation) when writing Dart code. Use this as your entry point to determine which rules to consult for specific coding scenarios.
alwaysApply: true
---

# Effective Dart

## When to Use

This guide helps you navigate the four Effective Dart rules and determine which to apply in different situations:

- **Starting a new Dart project or package** - Understand which rules apply to different aspects of development
- **Reviewing code** - Quickly identify which rules to reference for specific issues
- **Learning Dart conventions** - Build a mental model of how the rules work together
- **Generating or refactoring code** - Know which guidelines apply to your current task

The four Effective Dart rules are complementary and often overlap. This guide helps you find the right information quickly.

## Quick Reference

| Rule | Purpose | When to Use | Key Focus Areas |
|------|---------|-------------|-----------------|
| **[Style](effective-dart-style.mdc)** | Consistent naming and formatting | Naming anything, organizing imports, formatting code | `UpperCamelCase`, `lowerCamelCase`, `lowercase_with_underscores`, import order, `dart format` |
| **[Design](effective-dart-design.mdc)** | API consistency and usability | Designing public APIs, creating classes/functions, choosing types | API naming patterns, class modifiers, type annotations, parameters, equality |
| **[Usage](effective-dart-usage.mdc)** | Idiomatic implementation | Writing code bodies, using language features | Libraries, null safety, collections, async/await, error handling |
| **[Documentation](effective-dart-documentation.mdc)** | Clear, effective comments | Writing doc comments, documenting APIs | `///` comments, doc structure, parameter references, markdown in docs |

## Decision Framework

Use these questions to quickly identify which rule(s) to consult:

1. **Are you naming something?**
   - → **[Style](effective-dart-style.mdc)** for naming conventions (`UpperCamelCase`, `lowerCamelCase`, etc.)
   - → **[Design](effective-dart-design.mdc)** for API naming patterns (verbs for methods, noun phrases for properties)

2. **Are you designing a public API?**
   - → **[Design](effective-dart-design.mdc)** for API structure, class modifiers, type signatures
   - → **[Style](effective-dart-style.mdc)** for consistent naming across the API

3. **Are you implementing functionality?**
   - → **[Usage](effective-dart-usage.mdc)** for idiomatic patterns (collections, async, null safety)
   - → **[Design](effective-dart-design.mdc)** for type annotations and member design

4. **Are you writing comments or documentation?**
   - → **[Documentation](effective-dart-documentation.mdc)** for doc comment structure and content
   - → **[Design](effective-dart-design.mdc)** for what to document (parameters, exceptions, return values)

5. **Are you organizing imports or formatting code?**
   - → **[Style](effective-dart-style.mdc)** for import order and `dart format` usage

## Rules Overview

### Style: Naming and Formatting

**Focus:** Mechanical consistency in naming, import organization, and code formatting.

The Style rule ensures code looks consistent across the Dart ecosystem. It covers naming conventions for all identifiers, import ordering, and formatting guidelines. Most formatting is automated by `dart format`, but naming requires human attention.

**Key linter rules:** `camel_case_types`, `file_names`, `non_constant_identifier_names`, `directives_ordering`, `lines_longer_than_80_chars`

**Read:** [effective-dart-style.mdc](effective-dart-style.mdc)

### Design: API Structure and Usability

**Focus:** Making APIs consistent, predictable, and easy to use correctly.

The Design rule helps you create intuitive APIs through consistent naming patterns, appropriate type annotations, and thoughtful parameter design. It emphasizes user experience over implementation convenience.

**Key linter rules:** N/A (mostly judgment calls, not automatically enforceable)

**Read:** [effective-dart-design.mdc](effective-dart-design.mdc)

### Usage: Idiomatic Implementation

**Focus:** Using Dart language features effectively in code bodies.

The Usage rule covers how to implement functionality idiomatically. It includes guidance on libraries, null safety, collections, functions, error handling, and async programming. These patterns make code more maintainable and leverage Dart's strengths.

**Key linter rules:** `prefer_relative_imports`, `avoid_init_to_null`, `prefer_collection_literals`, `prefer_function_declarations_over_variables`, `unnecessary_this`

**Read:** [effective-dart-usage.mdc](effective-dart-usage.mdc)

### Documentation: Clear Comments

**Focus:** Writing documentation that helps users understand and use your code.

The Documentation rule ensures doc comments are clear, complete, and well-structured. It covers when to document, how to structure documentation, and how to write effective summaries and descriptions.

**Key linter rules:** N/A (quality of writing is subjective)

**Read:** [effective-dart-documentation.mdc](effective-dart-documentation.mdc)

## Common Scenarios

Here are concrete scenarios mapped to the relevant rules:

### Creating a new class

- **[Style](effective-dart-style.mdc):** Use `UpperCamelCase` for the class name
- **[Design](effective-dart-design.mdc):** Choose appropriate class modifiers (`final`, `interface`, `sealed`), design constructor API
- **[Usage](effective-dart-usage.mdc):** Implement members using idiomatic patterns
- **[Documentation](effective-dart-documentation.mdc):** Write `///` doc comment describing what an instance represents

### Naming a function

- **[Style](effective-dart-style.mdc):** Use `lowerCamelCase` for function names
- **[Design](effective-dart-design.mdc):** Use imperative verbs for side effects, noun phrases for returns
- **[Documentation](effective-dart-documentation.mdc):** Start doc comment with third-person verb

### Adding type annotations

- **[Design](effective-dart-design.mdc):** When to annotate (public APIs, uninitialized variables, non-obvious returns)
- **[Usage](effective-dart-usage.mdc):** When not to annotate (initialized locals, inferred parameters)

### Working with nullable types

- **[Usage](effective-dart-usage.mdc):** Use null-check patterns, avoid unnecessary `late`, use `??` operator
- **[Design](effective-dart-design.mdc):** Avoid returning nullable futures/collections

### Writing async code

- **[Usage](effective-dart-usage.mdc):** Prefer async/await over raw futures, use stream higher-order methods
- **[Design](effective-dart-design.mdc):** Use `Future<void>` not `Future` for no-return async functions

### Organizing imports

- **[Style](effective-dart-style.mdc):** Order: `dart:`, then `package:`, then relative; alphabetize within sections
- **[Usage](effective-dart-usage.mdc):** Prefer relative imports within `lib`, don't import from `src`

### Documenting parameters

- **[Documentation](effective-dart-documentation.mdc):** Reference with `[paramName]` in prose, don't use `@param` tags
- **[Design](effective-dart-design.mdc):** Avoid describing parameters in function name

### Handling errors

- **[Usage](effective-dart-usage.mdc):** Use `on` clauses, `rethrow` to preserve stack traces, throw `Error` for bugs
- **[Documentation](effective-dart-documentation.mdc):** Document thrown exceptions after blank line: "Throws [ExceptionType] if..."

### Creating collections

- **[Usage](effective-dart-usage.mdc):** Use collection literals (`[]`, `{}`), leverage spread and control flow
- **[Design](effective-dart-design.mdc):** Return empty collections instead of null

### Naming boolean properties

- **[Style](effective-dart-style.mdc):** Use `lowerCamelCase`
- **[Design](effective-dart-design.mdc):** Use non-imperative verb phrases (`isEnabled`, `hasElements`, `canClose`)
- **[Documentation](effective-dart-documentation.mdc):** Start doc comment with "Whether" followed by noun/gerund phrase

## Integration Guidance

### How Rules Work Together

The four rules are designed to be complementary, not competing:

- **Style + Design:** Style handles mechanics (capitalization, underscores), Design handles semantics (verb vs. noun)
- **Design + Usage:** Design defines the API contract, Usage implements it idiomatically
- **Documentation + All:** Documentation describes what the other three rules produce

### Typical Development Workflow

1. **Design phase:** Consult [Design](effective-dart-design.mdc) for API structure, naming patterns, and type signatures
2. **Implementation phase:** Apply [Usage](effective-dart-usage.mdc) for idiomatic code bodies
3. **Documentation phase:** Use [Documentation](effective-dart-documentation.mdc) for clear doc comments
4. **Formatting phase:** Run `dart format` and verify [Style](effective-dart-style.mdc) naming conventions

In practice, these phases overlap—you'll consult multiple rules simultaneously.

### Overlapping Guidance

Some topics appear in multiple rules with different emphasis:

- **Naming:** Style (mechanics) + Design (semantics)
- **Type annotations:** Design (when to annotate) + Usage (when not to)
- **Parameters:** Design (API design) + Documentation (how to describe)
- **Return types:** Design (API contracts) + Usage (implementation choices)

When rules overlap, apply both. For example, a boolean property should use:

- `lowerCamelCase` (Style)
- Non-imperative verb phrase like `isVisible` (Design)
- Doc comment starting with "Whether" (Documentation)

### AI Code Generation Tips

When generating Dart code:

1. **Start with Design:** Ensure API names and structure follow Design patterns
2. **Apply Usage patterns:** Implement with idiomatic Dart (collection literals, async/await)
3. **Add Documentation:** Write clear `///` doc comments for public APIs
4. **Verify Style:** Check naming conventions and run `dart format`

**Common AI pitfalls to avoid:**

- Using `get` prefix for methods (violates Design)
- Redundant type annotations on initialized locals (violates Usage)
- Missing `dart:` import section separator (violates Style)
- Using `@param` tags instead of prose (violates Documentation)

### When Rules Conflict

If guidance seems contradictory:

1. **Check context:** Rules target different aspects (API design vs. implementation vs. documentation)
2. **Prioritize specificity:** More specific guidance overrides general guidance
3. **Consider audience:** Design focuses on API users, Usage on implementers
4. **Apply both:** Often both rules apply to different aspects of the same code

Conflicts are rare because rules target different concerns. If truly stuck, prioritize Design for public APIs and Usage for private implementation.

## Reference

### Rule Files

- [effective-dart-style.mdc](effective-dart-style.mdc) - Naming and formatting conventions
- [effective-dart-design.mdc](effective-dart-design.mdc) - API design patterns
- [effective-dart-usage.mdc](effective-dart-usage.mdc) - Idiomatic implementation
- [effective-dart-documentation.mdc](effective-dart-documentation.mdc) - Doc comment guidelines

### Tools

- `dart format` - Official code formatter
- `dart analyze` - Static analyzer with linter rules
- `dart doc` - Documentation generator
