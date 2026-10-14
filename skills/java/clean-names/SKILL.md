---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, methods, classes, interfaces, or packages in Java. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
when_to_use: |
  Also trigger on: single-letter or cryptic identifiers (`d`, `x`, `proc`), Hungarian notation (`strName`, `lstUsers`, `iCount`), `I`-prefixed interfaces (`IUserRepository`), method names that hide side effects (e.g. `getConfig` that also writes a file), non-standard abbreviations, or asks like "rename this", "what does this variable mean", "clearer name".
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```java
// Bad - what is d?
int d = 86_400;

// Good - obvious meaning
int secondsPerDay = 86_400;

// Bad - what does this method do?
List<Integer> proc(List<Integer> values) {
    return values.stream().filter(value -> value > 0).toList();
}

// Good - intent is clear
List<Integer> filterPositiveNumbers(List<Integer> numbers) {
    return numbers.stream().filter(number -> number > 0).toList();
}
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or method.

```java
// Bad - too implementation-specific
Map<Long, String> getMapOfUserIdsToNames() {
    // ...
}

// Good - abstracts the data structure
Map<Long, String> getUserDirectory() {
    // ...
}
```

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known conventions.

```java
// Good - uses pattern name
class UserFactory {
    User create(UserData data) {
        // ...
    }
}

// Good - uses domain term
Money calculateAmortization(Money principal, Rate rate, Period term) {
    // ...
}
```

## N4: Unambiguous Names

Choose names that make the workings of a method or variable unambiguous.

```java
// Bad - ambiguous
void rename(Path source, Path target) {
    // ...
}

// Good - clear what's being renamed
void renameFile(Path oldPath, Path newPath) {
    // ...
}
```

## N5: Use Longer Names for Longer Scopes

Short names are fine for tiny scopes. Longer scopes need longer, more descriptive names.

```java
// Good - short name for tiny scope
int total = numbers.stream().mapToInt(n -> n).sum();

// Good - longer name for class-level constant
private static final int MAX_RETRY_ATTEMPTS_BEFORE_FAILURE = 5;

// Bad - short name at class level
private static final int MAX = 5;
```

## N6: Avoid Encodings

Don't encode type or scope information into names. Modern editors make this unnecessary.

```java
// Bad - Hungarian notation
String strName = "Alice";
List<User> lstUsers = new ArrayList<>();
int iCount = 0;

// Good - clean names
String name = "Alice";
List<User> users = new ArrayList<>();
int count = 0;

// Bad - interface prefix
interface IUserRepository {
    Optional<User> findById(long id);
}

// Good - just name it
interface UserRepository {
    Optional<User> findById(long id);
}
```

## N7: Names Should Describe Side Effects

If a method does something beyond what its name suggests, the name is misleading.

```java
// Bad - name doesn't mention file creation
Config getConfig(Path configPath) throws IOException {
    if (Files.notExists(configPath)) {
        Files.writeString(configPath, "{}"); // Hidden side effect!
    }
    return configParser.parse(Files.readString(configPath));
}

// Good - name reveals behavior
Config getOrCreateConfig(Path configPath) throws IOException {
    if (Files.notExists(configPath)) {
        Files.writeString(configPath, "{}");
    }
    return configParser.parse(Files.readString(configPath));
}
```

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `SECONDS_PER_DAY` not `d` |
| N2 | Right abstraction level | `getUserDirectory()` not `getMapOf...` |
| N3 | Standard nomenclature | `UserFactory`, `calculateAmortization` |
| N4 | Unambiguous | `renameFile(oldPath, newPath)` |
| N5 | Length matches scope | Short for loops, long for class fields |
| N6 | No encodings | `users` not `lstUsers` |
| N7 | Describe side effects | `getOrCreateConfig()` |
