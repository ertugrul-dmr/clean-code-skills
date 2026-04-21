---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, functions, classes, interfaces, or modules in TypeScript. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
when_to_use: |
  Also trigger on: single-letter or cryptic identifiers (`d`, `x`, `proc`), Hungarian notation (`strName`, `arrUsers`, `nCount`), `I`-prefixed interfaces (`IUserRepository`), function names that hide side effects (e.g. `getConfig` that also mutates state), ambiguous names like `rename(source, target)`, or asks like "rename this", "clearer name".
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```ts
// Bad - what is d?
const d = 86400;

// Good - obvious meaning
const SECONDS_PER_DAY = 86400;

// Bad - what does this function do?
function proc(values: number[]) {
  return values.filter((value) => value > 0);
}

// Good - intent is clear
function filterPositiveNumbers(numbers: number[]) {
  return numbers.filter((number) => number > 0);
}
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function.

```ts
// Bad - too implementation-specific
function getMapOfUserIdsToNames() {
  // ...
}

// Good - abstracts the data structure
function getUserDirectory() {
  // ...
}
```

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known conventions.

```ts
// Good - uses pattern name
class UserFactory {
  create(data: unknown) {
    // ...
  }
}

// Good - uses domain term
function calculateAmortization(principal: number, rate: number, term: number) {
  // ...
}
```

## N4: Unambiguous Names

Choose names that make the workings of a function or variable unambiguous.

```ts
// Bad - ambiguous
function rename(source: string, target: string) {
  // ...
}

// Good - clear what's being renamed
function renameFile(oldPath: string, newPath: string) {
  // ...
}
```

## N5: Use Longer Names for Longer Scopes

Short names are fine for tiny scopes. Longer scopes need longer, more descriptive names.

```ts
// Good - short name for tiny scope
const total = numbers.reduce((sum, n) => sum + n, 0);

// Good - longer name for module-level constant
const MAX_RETRY_ATTEMPTS_BEFORE_FAILURE = 5;

// Bad - short name at module level
const MAX = 5;
```

## N6: Avoid Encodings

Don't encode type or scope information into names. Modern editors make this unnecessary.

```ts
// Bad - Hungarian notation
const strName = "Alice";
const arrUsers: string[] = [];
const nCount = 0;

// Good - clean names
const name = "Alice";
const users: string[] = [];
const count = 0;

// Bad - interface prefix
interface IUserRepository {
  findById(id: string): Promise<unknown>;
}

// Good - just name it
interface UserRepository {
  findById(id: string): Promise<unknown>;
}
```

## N7: Names Should Describe Side Effects

If a function does something beyond what its name suggests, the name is misleading.

```ts
const configStore = new Map<string, string>();

// Bad - name doesn't mention file creation
function getConfig(configPath: string) {
  if (!configStore.has(configPath)) {
    configStore.set(configPath, "{}"); // Hidden side effect!
  }
  return JSON.parse(configStore.get(configPath) ?? "{}");
}

// Good - name reveals behavior
function getOrCreateConfig(configPath: string) {
  if (!configStore.has(configPath)) {
    configStore.set(configPath, "{}");
  }
  return JSON.parse(configStore.get(configPath) ?? "{}");
}
```

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `SECONDS_PER_DAY` not `d` |
| N2 | Right abstraction level | `getUserDirectory()` not `getMapOf...` |
| N3 | Standard nomenclature | `UserFactory`, `calculateAmortization` |
| N4 | Unambiguous | `renameFile(oldPath, newPath)` |
| N5 | Length matches scope | Short for loops, long for globals |
| N6 | No encodings | `users` not `arrUsers` |
| N7 | Describe side effects | `getOrCreateConfig()` |
