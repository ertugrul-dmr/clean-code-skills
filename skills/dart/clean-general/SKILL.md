---
name: clean-general
description: Use when writing, fixing, editing, or reviewing Dart code quality. Enforces Clean Code's core principles—DRY, single responsibility, clear intent, no magic numbers, proper abstractions.
when_to_use: |
  Also trigger on: duplicated logic across files or branches (G5), magic numbers or hardcoded values (G25), long if/else chains that should be polymorphism (G23), chained property access like `a.b.c.d` or long null-safe chains (G36), functions juggling multiple responsibilities (G30), clever one-liners whose intent is not obvious (G16).
---

# General Clean Code Principles

## Critical Rules

**G5: DRY (Don't Repeat Yourself)**

Every piece of knowledge has one authoritative representation.

```dart
// Bad - duplication
const taxRate = 0.0825;
final caTotal = subtotal * 1.0825;
final nyTotal = subtotal * 1.07;

// Good - single source of truth
const taxRates = {'CA': 0.0825, 'NY': 0.07};
double calculateTotal(double subtotal, String state) {
  return subtotal * (1 + taxRates[state]!);
}
```

**G16: No Obscured Intent**

Don't be clever. Be clear.

```dart
// Bad - what does this do?
return ((x & 0x0f) << 4) | (y & 0x0f);

// Good - obvious intent
return packCoordinates(x, y);
```

**G23: Prefer Polymorphism to If/Else**

```dart
// Bad - will grow forever
double calculatePay(Employee employee) {
  if (employee.type == EmployeeType.salaried) {
    return employee.salary!;
  } else if (employee.type == EmployeeType.hourly) {
    return employee.hours! * employee.rate!;
  } else if (employee.type == EmployeeType.commissioned) {
    return employee.base! + employee.commission!;
  }
  return 0;
}

// Good - open/closed principle
abstract class Employee {
  double calculatePay();
}

class SalariedEmployee implements Employee {
  const SalariedEmployee(this.salary);
  final double salary;

  @override
  double calculatePay() => salary;
}

class HourlyEmployee implements Employee {
  const HourlyEmployee(this.hours, this.rate);
  final double hours;
  final double rate;

  @override
  double calculatePay() => hours * rate;
}

class CommissionedEmployee implements Employee {
  const CommissionedEmployee(this.base, this.commission);
  final double base;
  final double commission;

  @override
  double calculatePay() => base + commission;
}
```

**G25: Replace Magic Numbers with Named Constants**

```dart
// Bad
if (elapsedTime > 86400) {
  // ...
}

// Good
const secondsPerDay = 86400;
if (elapsedTime > secondsPerDay) {
  // ...
}
```

**G30: Functions Should Do One Thing**

If you can extract another function, your function does more than one thing.

**G36: Law of Demeter (Avoid Train Wrecks)**

```dart
// Bad - reaching through multiple objects
final outputDir = context.options.scratchDir.absolutePath;

// Good - one dot
final outputDir = context.getScratchDir();
```

## Enforcement Checklist

When reviewing AI-generated code, verify:
- [ ] No duplication (G5)
- [ ] Clear intent, no magic numbers (G16, G25)
- [ ] Polymorphism over conditionals (G23)
- [ ] Functions do one thing (G30)
- [ ] No Law of Demeter violations (G36)
- [ ] Boundary conditions handled (G3)
- [ ] Dead code removed (G9)
