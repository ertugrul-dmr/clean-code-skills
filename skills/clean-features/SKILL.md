---
name: clean-features
description: Use when planning, writing, fixing, editing, or reviewing new features and changes in the existing code-base. Enforces reuse and extension of previous logic, prevents huge monolith files.
---

# Clean Features

## FE1: Don't reinvent the wheel

Some functionality that you want to implement might already exist in the code. Duplicating it again (even if a small
tweak is needed) will cause headaches in maintenance and troubleshooting. Always reuse existing logic and extend
on them if tweaks are needed but never repeat same core logic in multiple places.

## FE2: Appropriation is key

While it may seem tempting to put all new logic into an existing file or module, take a step back. Does it
at all belong there? Will it make the codebase cleaner and more manageable if it exists in a separate module entirely?
Never bloat files for the sake of it; always group and place related code into their appropriate modules, even if it
means creating new modules.

## FE3: Size matters

Well, in case of cognitive complexity and maintainability, code size does matter! Continuing from FE2, it is important
to not write giant, monolithic files by cramming everything into one place. Use appropriation and break unrelated logic
into their own purpose files. When files start to exceed 700 lines, it is a good time to consider if splitting it
makes sense.

## FE4: No spaghetti

Keeping the code DRY is good and when multiple modules tend to reuse the same functionality, move them out into a common
utilities or shared module. Shared code existing deep in separate individual modules is time-consuming to find and
maintain.

## The Goal

The best features introduce minimal additions to the codebase and does not increase its complexity ten-fold. We must
strive to write clean, explainable, maintainable features while being the least intrusive and disruptive to the existing
codebase.
