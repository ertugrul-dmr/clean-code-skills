---
name: clean-tests
description: Use when writing, fixing, editing, or refactoring Dart tests. Enforces Clean Code principles—fast tests, boundary coverage, one assert per test.
when_to_use: |
  Also trigger on: slow or flaky tests, `skip:` without a clear reason, tests that only cover the happy path, tests with multiple assertions about different concepts, missing boundary cases (empty collections, off-by-one, page zero), or asks about "coverage gap", "edge case", "did we test".
---

# Clean Tests

## T1: Insufficient Tests

Test everything that could possibly break. Use coverage tools as a guide, not a goal.

```dart
// Bad - only tests happy path
test('divide', () {
  expect(divide(10, 2), equals(5));
});

// Good - tests edge cases too
test('divide normal', () {
  expect(divide(10, 2), equals(5));
});

test('divide by zero', () {
  expect(() => divide(10, 0), throwsA(isA<ArgumentError>()));
});

test('divide negative', () {
  expect(divide(-10, 2), equals(-5));
});
```

## T2: Use a Coverage Tool

Coverage tools report gaps in your testing strategy. Don't ignore them.

```bash
# Run with coverage
dart test --coverage=coverage
dart pub global run coverage:format_coverage --lcov --in=coverage --out=coverage/lcov.info

# Flutter projects
flutter test --coverage

# Aim for meaningful coverage, not 100%
```

## T3: Don't Skip Trivial Tests

Trivial tests document behavior and catch regressions. They're worth more than their cost.

```dart
// Worth having - documents expected behavior
test('user default role', () {
  final user = User(name: 'Alice');
  expect(user.role, equals('member'));
});
```

## T4: An Ignored Test Is a Question About an Ambiguity

Don't use `skip:` to hide problems. Either fix the test or delete it.

```dart
// Bad - hiding a problem
test('async operation', () {
  // flaky, fix later
}, skip: true);

// Good - either fix it or document why it's skipped
test('cache invalidation', () {
  // ...
}, skip: 'Requires Redis — see CONTRIBUTING.md for setup');
```

## T5: Test Boundary Conditions

Bugs congregate at boundaries. Test them explicitly.

```dart
test('pagination boundaries', () {
  final items = List.generate(100, (i) => i);

  // First page
  expect(paginate(items, page: 1, size: 10), equals(items.sublist(0, 10)));

  // Last page
  expect(paginate(items, page: 10, size: 10), equals(items.sublist(90, 100)));

  // Beyond last page
  expect(paginate(items, page: 11, size: 10), isEmpty);

  // Page zero (invalid)
  expect(() => paginate(items, page: 0, size: 10), throwsA(isA<RangeError>()));

  // Empty list
  expect(paginate([], page: 1, size: 10), isEmpty);
});
```

## T6: Exhaustively Test Near Bugs

When you find a bug, write tests for all similar cases. Bugs cluster.

```dart
// Found bug: off-by-one in date calculation
// Now test ALL date boundaries
test('month boundaries', () {
  expect(lastDayOfMonth(2024, 1), equals(31));  // January
  expect(lastDayOfMonth(2024, 2), equals(29));  // Leap year February
  expect(lastDayOfMonth(2023, 2), equals(28));  // Non-leap February
  expect(lastDayOfMonth(2024, 4), equals(30));  // 30-day month
  expect(lastDayOfMonth(2024, 12), equals(31)); // December
});
```

## T7: Patterns of Failure Are Revealing

When tests fail, look for patterns. They often point to deeper issues.

```dart
// If all async tests fail intermittently,
// the problem isn't the tests—it's the async handling
```

## T8: Test Coverage Patterns Can Be Revealing

Look at which code paths are untested. Often they reveal design problems.

```dart
// If you can't easily test a function, it probably does too much
// Refactor for testability
```

## T9: Tests Should Be Fast

Slow tests don't get run. Keep unit tests under 100ms each.

```dart
// Bad - hits real database
test('user creation', () async {
  final db = await connectToDatabase(); // Slow!
  final user = await db.createUser('Alice');
  expect(user.name, equals('Alice'));
});

// Good - uses mock or in-memory
test('user creation', () async {
  final db = InMemoryDatabase();
  final user = await db.createUser('Alice');
  expect(user.name, equals('Alice'));
});
```

## Test Organization

### F.I.R.S.T. Principles

- **Fast**: Tests should run quickly
- **Independent**: Tests shouldn't depend on each other
- **Repeatable**: Same result every time, any environment
- **Self-Validating**: Pass or fail, no manual inspection
- **Timely**: Written before or with the code, not after

### One Concept Per Test

```dart
// Bad - testing multiple things
test('user', () {
  final user = User(name: 'Alice', email: 'alice@example.com');
  expect(user.name, equals('Alice'));
  expect(user.email, equals('alice@example.com'));
  expect(user.isValid(), isTrue);
  user.activate();
  expect(user.isActive, isTrue);
});

// Good - one concept each
test('user stores name', () {
  final user = User(name: 'Alice', email: 'alice@example.com');
  expect(user.name, equals('Alice'));
});

test('user stores email', () {
  final user = User(name: 'Alice', email: 'alice@example.com');
  expect(user.email, equals('alice@example.com'));
});

test('new user is valid', () {
  final user = User(name: 'Alice', email: 'alice@example.com');
  expect(user.isValid(), isTrue);
});

test('user can be activated', () {
  final user = User(name: 'Alice', email: 'alice@example.com');
  user.activate();
  expect(user.isActive, isTrue);
});
```

## Quick Reference

| Rule | Principle |
|------|-----------|
| T1 | Test everything that could break |
| T2 | Use coverage tools |
| T3 | Don't skip trivial tests |
| T4 | Ignored test = ambiguity question |
| T5 | Test boundary conditions |
| T6 | Exhaustively test near bugs |
| T7 | Look for patterns in failures |
| T8 | Check coverage when debugging |
| T9 | Tests must be fast (<100ms) |
