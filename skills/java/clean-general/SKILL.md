---
name: clean-general
description: Use when writing, fixing, editing, or reviewing Java code quality. Enforces Clean Code's core principles—DRY, single responsibility, clear intent, no magic numbers, proper abstractions.
when_to_use: |
  Also trigger on: duplicated logic across files or branches (G5), magic numbers or hardcoded values (G25), long if/else or switch chains that should be polymorphism (G23), chained method calls like `a.getB().getC().getD()` (G36), methods juggling multiple responsibilities (G30), clever expressions whose intent is not obvious (G16).
---

# General Clean Code Principles

## Critical Rules

**G5: DRY (Don't Repeat Yourself)**

Every piece of knowledge has one authoritative representation.

```java
// Bad - duplication
double taxRate = 0.0825;
double caTotal = subtotal * 1.0825;
double nyTotal = subtotal * 1.07;

// Good - single source of truth
private static final Map<String, Double> TAX_RATES = Map.of(
        "CA", 0.0825,
        "NY", 0.07);

double calculateTotal(double subtotal, String state) {
    return subtotal * (1 + TAX_RATES.get(state));
}
```

**G16: No Obscured Intent**

Don't be clever. Be clear.

```java
// Bad - what does this do?
return (x & 0x0F) << 4 | (y & 0x0F);

// Good - obvious intent
return packCoordinates(x, y);
```

**G23: Prefer Polymorphism to If/Else**

```java
// Bad - will grow forever
double calculatePay(Employee employee) {
    if (employee.type() == EmployeeType.SALARIED) {
        return employee.salary();
    } else if (employee.type() == EmployeeType.HOURLY) {
        return employee.hours() * employee.rate();
    } else if (employee.type() == EmployeeType.COMMISSIONED) {
        return employee.base() + employee.commission();
    }
    throw new IllegalArgumentException("Unknown employee type");
}

// Good - open/closed principle
interface Employee {
    double calculatePay();
}

record SalariedEmployee(double salary) implements Employee {
    @Override
    public double calculatePay() {
        return salary;
    }
}

record HourlyEmployee(double hours, double rate) implements Employee {
    @Override
    public double calculatePay() {
        return hours * rate;
    }
}

record CommissionedEmployee(double base, double commission) implements Employee {
    @Override
    public double calculatePay() {
        return base + commission;
    }
}
```

**G25: Replace Magic Numbers with Named Constants**

```java
// Bad
if (elapsedTime > 86_400) {
    // ...
}

// Good
private static final int SECONDS_PER_DAY = 86_400;
if (elapsedTime > SECONDS_PER_DAY) {
    // ...
}
```

**G30: Functions Should Do One Thing**

If you can extract another method, your method does more than one thing.

**G36: Law of Demeter (Avoid Train Wrecks)**

```java
// Bad - reaching through multiple objects
Path outputDir = context.getOptions().getScratchDir().toAbsolutePath();

// Good - one dot
Path outputDir = context.getScratchDir();
```

## Enforcement Checklist

When reviewing AI-generated code, verify:
- [ ] No duplication (G5)
- [ ] Clear intent, no magic numbers (G16, G25)
- [ ] Polymorphism over conditionals (G23)
- [ ] Methods do one thing (G30)
- [ ] No Law of Demeter violations (G36)
- [ ] Boundary conditions handled (G3)
- [ ] Dead code removed (G9)
