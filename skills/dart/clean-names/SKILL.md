---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, functions, classes, or libraries in Dart. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
when_to_use: |
  Also trigger on: single-letter or cryptic identifiers (`d`, `x`, `proc`), Hungarian notation (`strName`, `listUsers`, `intCount`), `I`-prefixed abstract classes, function names that hide side effects (e.g. `getConfig` that also writes a file), non-standard abbreviations, or asks like "rename this", "what does this variable mean", "clearer name".
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```dart
// Bad - what is d?
final d = 86400;

// Good - obvious meaning
const secondsPerDay = 86400;

// Bad - what does this function do?
List<int> proc(List<int> lst) {
  return lst.where((x) => x > 0).toList();
}

// Good - intent is clear
List<int> filterPositiveNumbers(List<int> numbers) {
  return numbers.where((n) => n > 0).toList();
}
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function.

```dart
// Bad - too implementation-specific
Map<int, String> getMapOfUserIdsToNames() {
  // ...
}

// Good - abstracts the data structure
Map<int, String> getUserDirectory() {
  // ...
}
```

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known conventions.

```dart
// Good - uses pattern name
class UserFactory {
  User create(Map<String, dynamic> data) { ... }
}

// Good - uses domain term
double calculateAmortization(double principal, double rate, int term) { ... }
```

## N4: Unambiguous Names

Choose names that make the workings of a function or variable unambiguous.

```dart
// Bad - ambiguous
void rename(String oldName, String newName) {
  // ...
}

// Good - clear what's being renamed
void renameFile(String oldPath, String newPath) {
  // ...
}
```

## N5: Use Longer Names for Longer Scopes

Short names are fine for tiny scopes. Longer scopes need longer, more descriptive names.

```dart
// Good - short name for tiny scope
final total = numbers.reduce((sum, n) => sum + n);

// Good - longer name for top-level constant
const maxRetryAttemptsBeforeFailure = 5;

// Bad - short name at top level
const max = 5;
```

## N6: Avoid Encodings

Don't encode type or scope information into names. Modern editors make this unnecessary.

```dart
// Bad - Hungarian notation
final strName = 'Alice';
final listUsers = <String>[];
final intCount = 0;

// Good - clean names
final name = 'Alice';
final users = <String>[];
final count = 0;

// Bad - interface prefix (Dart uses abstract classes, not I-prefixed names)
abstract class IUserRepository {
  Future<User> findById(int id);
}

// Good - just name it
abstract class UserRepository {
  Future<User> findById(int id);
}
```

## N7: Names Should Describe Side Effects

If a function does something beyond what its name suggests, the name is misleading.

```dart
final _configCache = <String, String>{};

// Bad - name doesn't mention cache population
String getConfig(String key) {
  if (!_configCache.containsKey(key)) {
    _configCache[key] = '{}'; // Hidden side effect!
  }
  return _configCache[key]!;
}

// Good - name reveals behavior
String getOrCreateConfig(String key) {
  if (!_configCache.containsKey(key)) {
    _configCache[key] = '{}';
  }
  return _configCache[key]!;
}
```

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `secondsPerDay` not `d` |
| N2 | Right abstraction level | `getUserDirectory()` not `getMapOf...` |
| N3 | Standard nomenclature | `UserFactory`, `calculateAmortization` |
| N4 | Unambiguous | `renameFile(oldPath, newPath)` |
| N5 | Length matches scope | Short for loops, long for top-level |
| N6 | No encodings | `users` not `listUsers` |
| N7 | Describe side effects | `getOrCreateConfig()` |
