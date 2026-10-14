---
name: boy-scout
description: Use when fixing, editing, changing, debugging, or working with any Java code. Applies the Boy Scout Rule—always leave code cleaner than you found it. Orchestrates other clean code skills as needed.
when_to_use: |
  Also trigger on: "while you're at it", "any quick wins", "improve this a bit", "anything else obviously wrong", or when editing existing Java and an adjacent small cleanup is possible alongside the asked-for change.
---

# The Boy Scout Rule

> "Always leave the campground cleaner than you found it."
> — Robert Baden-Powell

> "Always check a module in cleaner than when you checked it out."
> — Robert C. Martin, *Clean Code*

## The Philosophy

You don't have to make every module perfect. You simply have to make it **a little bit better** than when you found it.

If we all followed this simple rule:
- Our systems would gradually get better as they evolved
- Teams would care for the system as a whole
- The relentless deterioration of software would end

## When Working on Code

Every time you touch code, look for **at least one small improvement**:

### Quick Wins (Do These Immediately)
- Rename a poorly named variable → triggers `clean-names`
- Delete a redundant comment → triggers `clean-comments`
- Remove dead code or unused imports
- Replace a magic number with a named constant
- Extract a deeply nested block into a well-named method

### Deeper Improvements (When Time Allows)
- Split a method that does multiple things → triggers `clean-functions`
- Remove duplication (DRY) → triggers `clean-general`
- Add missing boundary checks
- Improve test coverage → triggers `clean-tests`

## The Rule in Practice

```java
// You're asked to fix a bug in this method:
List<Double> proc(List<Double> d, List<Double> x, boolean flag) {
    // process data
    for (double i : d) {
        if (i > 0) {
            if (flag) {
                x.add(i * 1.0825); // tax
            } else {
                x.add(i);
            }
        }
    }
    return x;
}

// Don't just fix the bug and leave.
// Leave it cleaner:
private static final double TAX_RATE = 0.0825;

List<Double> processPositiveValuesWithTax(List<Double> values) {
    return values.stream()
            .filter(value -> value > 0)
            .map(value -> value * (1 + TAX_RATE))
            .toList();
}
```

**What changed:**
- ✅ Descriptive method name (N1)
- ✅ Clear parameter names (N1)
- ✅ Explicit types on the method interface
- ✅ Named constant for magic number (G25)
- ✅ No output argument mutation (F2)
- ✅ No flag argument (F3)

## Skill Orchestration

This skill coordinates with specialized skills based on what you're doing:

| Task | Trigger Skill |
|------|---------------|
| Writing/reviewing any Java | `java-clean-code` (master) |
| Naming variables, methods, classes | `clean-names` |
| Writing or editing comments | `clean-comments` |
| Creating or refactoring methods | `clean-functions` |
| Reviewing code quality | `clean-general` |
| Writing or reviewing tests | `clean-tests` |

## The Mindset

**Don't:**
- Leave code worse than you found it
- Say "that's not my code"
- Wait for a dedicated refactoring sprint
- Make massive changes unrelated to your task

**Do:**
- Make one small improvement with every commit
- Fix what you see, even if you didn't break it
- Keep changes proportional to your task
- Leave a trail of quality improvements

## AI Behavior

When working on code:
1. Complete the requested task first
2. Identify at least one small cleanup opportunity
3. Apply the appropriate specialized skill
4. Note the improvement made (e.g., "Also cleaned up: renamed `x` to `results` for clarity")

When reviewing code:
1. Load `java-clean-code` for comprehensive rule checking
2. Flag violations by rule number
3. Suggest incremental improvements, not complete rewrites

## The Boy Scout Promise

Every piece of code you touch gets a little better. Not perfect—just better.

Over time, better compounds into excellent.
