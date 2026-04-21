---
name: clean-tests
description: Use when writing, fixing, editing, or refactoring TypeScript tests. Enforces Clean Code principles—fast tests, boundary coverage, one assert per test.
when_to_use: |
  Also trigger on: slow or flaky tests, `test.skip`/`it.skip`/`.todo` without a clear reason, `test.only` left in committed code, tests that only cover the happy path, tests with multiple assertions about different concepts, missing boundary cases (empty arrays, off-by-one, page zero), or asks about "coverage gap" / "edge case".
---

# Clean Tests

## T1: Insufficient Tests

Test everything that could possibly break. Use coverage tools as a guide, not a goal.

```ts
// Bad - only tests happy path
test("divide", () => {
  expect(divide(10, 2)).toBe(5);
});

// Good - tests edge cases too
test("divide normal", () => {
  expect(divide(10, 2)).toBe(5);
});

test("divide by zero", () => {
  expect(() => divide(10, 0)).toThrow(RangeError);
});

test("divide negative", () => {
  expect(divide(-10, 2)).toBe(-5);
});
```

## T2: Use a Coverage Tool

Coverage tools report gaps in your testing strategy. Don't ignore them.

```bash
# Run with coverage
vitest run --coverage

# Aim for meaningful coverage, not 100%
```

## T3: Don't Skip Trivial Tests

Trivial tests document behavior and catch regressions. They're worth more than their cost.

```ts
// Worth having - documents expected behavior
test("user default role", () => {
  const user = new User("Alice");
  expect(user.role).toBe("member");
});
```

## T4: An Ignored Test Is a Question About an Ambiguity

Don't use `test.skip` to hide problems. Either fix the test or delete it.

```ts
// Bad - hiding a problem
test.skip("async operation", () => {
  // flaky, fix later
});

// Good - either fix it or document why it's skipped
test.skip("cache invalidation - requires Redis (see CONTRIBUTING.md)", () => {
});
```

## T5: Test Boundary Conditions

Bugs congregate at boundaries. Test them explicitly.

```ts
test("pagination boundaries", () => {
  const items = Array.from({ length: 100 }, (_, i) => i);

  // First page
  expect(paginate(items, 1, 10)).toEqual(items.slice(0, 10));

  // Last page
  expect(paginate(items, 10, 10)).toEqual(items.slice(90, 100));

  // Beyond last page
  expect(paginate(items, 11, 10)).toEqual([]);

  // Page zero (invalid)
  expect(() => paginate(items, 0, 10)).toThrow(RangeError);

  // Empty list
  expect(paginate([], 1, 10)).toEqual([]);
});
```

## T6: Exhaustively Test Near Bugs

When you find a bug, write tests for all similar cases. Bugs cluster.

```ts
// Found bug: off-by-one in date calculation
// Now test ALL date boundaries
test("month boundaries", () => {
  expect(lastDayOfMonth(2024, 1)).toBe(31); // January
  expect(lastDayOfMonth(2024, 2)).toBe(29); // Leap year February
  expect(lastDayOfMonth(2023, 2)).toBe(28); // Non-leap February
  expect(lastDayOfMonth(2024, 4)).toBe(30); // 30-day month
  expect(lastDayOfMonth(2024, 12)).toBe(31); // December
});
```

## T7: Patterns of Failure Are Revealing

When tests fail, look for patterns. They often point to deeper issues.

```ts
// If all async tests fail intermittently,
// the problem isn't the tests—it's the async handling
```

## T8: Test Coverage Patterns Can Be Revealing

Look at which code paths are untested. Often they reveal design problems.

```ts
// If you can't easily test a function, it probably does too much
// Refactor for testability
```

## T9: Tests Should Be Fast

Slow tests don't get run. Keep unit tests under 100ms each.

```ts
// Bad - hits real database
test("user creation", async () => {
  const db = await connectToDatabase(); // Slow!
  const user = await db.createUser("Alice");
  expect(user.name).toBe("Alice");
});

// Good - uses mock or in-memory
test("user creation", async () => {
  const db = new InMemoryDatabase();
  const user = await db.createUser("Alice");
  expect(user.name).toBe("Alice");
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

```ts
// Bad - testing multiple things
test("user", () => {
  const user = new User("Alice", "alice@example.com");
  expect(user.name).toBe("Alice");
  expect(user.email).toBe("alice@example.com");
  expect(user.isValid()).toBe(true);
  user.activate();
  expect(user.isActive).toBe(true);
});

// Good - one concept each
test("user stores name", () => {
  const user = new User("Alice", "alice@example.com");
  expect(user.name).toBe("Alice");
});

test("user stores email", () => {
  const user = new User("Alice", "alice@example.com");
  expect(user.email).toBe("alice@example.com");
});

test("new user is valid", () => {
  const user = new User("Alice", "alice@example.com");
  expect(user.isValid()).toBe(true);
});

test("user can be activated", () => {
  const user = new User("Alice", "alice@example.com");
  user.activate();
  expect(user.isActive).toBe(true);
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
