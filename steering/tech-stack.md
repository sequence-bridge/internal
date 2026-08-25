# Tech stack

## Languages
<which programming languages and runtime versions are in use>

-

## Components
Each major piece of the system, what it does, and the library/framework chosen for it.

| Component | Purpose | Library / framework |
|---|---|---|
| <name> | <what it does> | <choice> |

### How they connect
<data flow, module boundaries, key interfaces ΓÇö the architecture diagram in prose>

## Conventions
- **Formatter**: <tool>
- **Linter**: <tool>
- **Test runner**: <tool>
- **Folder structure**: <rules>
- **Naming**: <rules>
- **Database**: <dev / prod choices, migration policy>

## TDD-first
This project is TDD. Every feature starts with failing tests derived from the acceptance criteria in its spec. No implementation code lands before its test does. If a test is hard to write, the design is wrong ΓÇö fix the design, not the test.

(If this project is *not* TDD-first, replace this section with the actual testing policy. Don't leave aspirational TDD wording in place ΓÇö it rots fast.)
