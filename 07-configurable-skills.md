# Section 7: Configurable Skills

> **EXPERIMENTAL**
>
> This section describes an advanced pattern for creating skills that adapt to different projects.
> The pattern is conceptually sound but the concrete implementations (example skills) will be
> delivered in Epic 3.
>
> **Current Status:** Pattern documented, examples pending.

---

## The Problem: Hardcoded Conventions Kill Reusability

Skills are meant to be portable—write once, use everywhere. But in practice, many skills become
project-specific because they hardcode local conventions.

### Example: A Non-Portable Skill

```markdown
# Hexagonal Architecture Skill

When implementing Hexagonal Architecture:

1. Put domain logic in `src/domain/`
2. Put ports (interfaces) in `src/domain/ports/`
3. Put adapters in `src/infrastructure/adapters/`
4. Put use cases in `src/application/`
```

**The Problem:** These paths work for _this_ project, but the next project might use:

- `core/` instead of `src/domain/`
- `interfaces/` instead of `ports/`
- A completely different directory structure

If you share this skill, it won't work. You've mixed **Category 4 (Universal Pattern)** with
**Category 5 (Project-Specific Conventions)**.

---

## The Solution: Separate "Universal How" from "Local Where"

Configurable skills solve this by separating the _pattern_ from the _configuration_.

### The Pattern (Universal)

```markdown
# Hexagonal Architecture Skill

When implementing Hexagonal Architecture:

1. Domain logic goes in the **domain directory**
2. Port interfaces go in the **ports directory**
3. Adapter implementations go in the **adapters directory**
4. Use case orchestration goes in the **application directory**
```

### The Configuration (Project-Specific)

```yaml
# Configuration
domain_dir: src/domain
ports_dir: src/domain/ports
adapters_dir: src/infrastructure/adapters
application_dir: src/application
```

### The Result

The skill reads its configuration and applies the pattern using _this project's_ conventions.
The same skill works in any project—just change the configuration.

---

## Why Configure? The Precision-Reusability Breakthrough

Configuration solves a fundamental dilemma in AI memory:

| Without Configuration                | With Configuration                    |
| ------------------------------------ | ------------------------------------- |
| **Reusable** skills are vague        | **Pattern** is reusable AND precise   |
| **Precise** skills are project-bound | **Configuration** is project-specific |
| You must choose one                  | You get **both**                      |

### The Same Skill, Two Projects

**Project A** (E-commerce):

```yaml
paths:
  domain_dir: src/core/domain
  application_dir: src/core/use-cases
```

**Project B** (Healthcare API):

```yaml
paths:
  domain_dir: lib/domain
  application_dir: lib/services
```

Same Hexagonal Architecture skill, different structures. The skill teaches _how_ to implement
the pattern; the configuration says _where_ in this project.

---

## Benefits of Configurable Skills

### 1. True Portability

Write the skill once, configure per project. No more copying and editing.

### 2. Separation of Concerns

- The **skill author** focuses on the pattern (the "how")
- The **skill user** provides the configuration (the "where")

### 3. Team Consistency

Everyone uses the same skill with the same configuration. No drift between team members.

### 4. Easier Maintenance

Update the skill in one place, all projects benefit. Configuration stays local.

### 5. Self-Documenting

The configuration block explicitly declares what the skill needs from the project.

---

## Configuration Schema Format

### Basic Structure

A configurable skill has a `# Configuration` section with a YAML block:

````markdown
# Skill Name

## Purpose

What this skill does.

## Configuration

```yaml
# Required
domain_dir: src/domain # Where domain logic lives
ports_dir: src/domain/ports # Where port interfaces live

# Optional (with defaults)
adapters_dir: src/infrastructure # Where adapters live (default: src/adapters)
use_barrel_files: true # Whether to use index.ts exports (default: true)
```
````

## Instructions

[Instructions that reference config values like "the domain directory"]

````

### Schema Elements

| Element | Description | Example |
|---------|-------------|---------|
| **Key** | Configuration variable name | `domain_dir` |
| **Value** | Project-specific value | `src/domain` |
| **Comment** | Description/purpose | `# Where domain logic lives` |
| **Default** | Fallback if not specified | `(default: src/adapters)` |

### Required vs. Optional

```yaml
# Required - skill cannot function without these
domain_dir: ???                   # REQUIRED: Where domain logic lives
ports_dir: ???                    # REQUIRED: Where port interfaces live

# Optional - skill has sensible defaults
file_extension: .ts               # Optional (default: .ts)
use_barrel_files: true            # Optional (default: true)
```
````

Skills should:

- Fail clearly if required config is missing
- Use sensible defaults for optional config
- Document what each config key controls

### Type Hints

While not enforced, document expected types in comments:

```yaml
# Paths
domain_dir: src/domain # string: directory path
ports_dir: src/domain/ports # string: directory path

# Booleans
use_barrel_files: true # boolean: true/false
strict_mode: false # boolean: true/false

# Lists
allowed_extensions: # list: file extensions
  - .ts
  - .tsx

# Numbers
max_file_lines: 500 # number: maximum lines per file
```

---

## Self-Initialization Patterns

Configurable skills can get their configuration in three ways:

### Pattern 1: Scan Mode

The skill examines the codebase to infer configuration values.

**How it works:**

1. Skill is invoked without configuration
2. Skill scans project structure (package.json, directory layout, etc.)
3. Skill infers configuration values
4. Skill proceeds with inferred values

**Best for:**

- Projects with standard structures (Next.js, Rails, etc.)
- Configuration that can be reliably detected
- Reducing friction for first-time use

**Example Scan Logic:**

```markdown
## Self-Initialization

If configuration is not provided, this skill will attempt to detect values:

1. Check for `src/domain/` → set `domain_dir: src/domain`
2. Check for `core/` → set `domain_dir: core`
3. Check for `app/domain/` → set `domain_dir: app/domain`
4. If none found → prompt user
```

### Pattern 2: Prompt Mode

The skill asks the user for configuration on first use.

**How it works:**

1. Skill is invoked without configuration
2. Skill prompts user for each required value
3. Skill can write configuration to the skill file for future use
4. Skill proceeds with provided values

**Best for:**

- Non-standard project structures
- Configuration that can't be reliably detected
- Ensuring explicit user intent

**Example Prompt:**

```markdown
## Self-Initialization

This skill requires configuration. Please provide:

1. **Domain directory**: Where should domain logic go?
   Example: `src/domain`, `core`, `lib/domain`

2. **Ports directory**: Where should port interfaces go?
   Example: `src/domain/ports`, `core/interfaces`

3. **Adapters directory**: Where should adapters go?
   Example: `src/infrastructure`, `lib/adapters`
```

### Pattern 3: Hybrid (Scan + Confirm)

The skill scans, then confirms with the user.

**How it works:**

1. Skill scans project structure
2. Skill presents inferred values to user
3. User confirms or corrects
4. Skill proceeds with confirmed values

**Best for:**

- Balancing automation with user control
- Catching misdetections
- Building user confidence

**Example Hybrid:**

```markdown
## Self-Initialization

I detected the following structure:

- Domain directory: `src/domain` ✓
- Ports directory: `src/domain/ports` ✓
- Adapters directory: `src/infrastructure` ✓

Does this look correct? If not, please provide corrections.
```

### When to Use Each Pattern

| Pattern    | Use When                                       |
| ---------- | ---------------------------------------------- |
| **Scan**   | Standard frameworks, high confidence detection |
| **Prompt** | Unusual structures, critical configuration     |
| **Hybrid** | Balance of convenience and control             |

---

## Structure Template

### SKILL.md Template

````yaml
---
name: {skill-name}
description: |
  {What this skill does}.
  Use when {triggers/scenarios}.
  Configurable: requires project-specific paths.
allowed-tools: Read, Grep, Glob
---

# {Skill Name}

> **Configurable Skill**: This skill requires project-specific configuration.
> See Configuration section below.

## Purpose

{Brief description of what this skill accomplishes.}

## Configuration

```yaml
# Required
{key1}: {example_value}          # {description}
{key2}: {example_value}          # {description}

# Optional
{key3}: {default_value}          # {description} (default: {default})
```

## Self-Initialization

{Describe how the skill gets its configuration if not provided.}

Option 1: Scan

- Look for {pattern} → set {key1}
- Look for {pattern} → set {key2}

Option 2: Prompt

- Ask user for {key1}: "{question}"
- Ask user for {key2}: "{question}"

## Instructions

### Step 1: {First Step}

{Instructions referencing config like "the domain directory" or "configured ports path"}

### Step 2: {Second Step}

{More instructions...}

## Examples

### Example 1: {Scenario}

{Show the skill applied with sample configuration}

## Validation

{How to verify the skill was applied correctly}

````

### README.md Companion Template

For skills distributed as packages or shared across teams:

````markdown
# {Skill Name}

{Brief description}

## Installation

Copy the `{skill-name}/` folder to your project's `.claude/skills/` directory.

## Configuration

Before first use, configure the skill by editing `SKILL.md`:

```yaml
# Configuration
{ key1 }: { your_value } # {what this controls}
{ key2 }: { your_value } # {what this controls}
```

## Usage

{How to invoke the skill}

## Configuration Reference

| Key      | Required | Default     | Description   |
| -------- | -------- | ----------- | ------------- |
| `{key1}` | Yes      | -           | {description} |
| `{key2}` | Yes      | -           | {description} |
| `{key3}` | No       | `{default}` | {description} |

## Examples

{Show the skill in action with different configurations}
````

---

## Example: Hexagonal Architecture Skill

Here's what a complete configurable skill looks like:

````yaml
---
name: hexagonal-architecture
description: |
  Implements Hexagonal Architecture (Ports and Adapters) pattern.
  Use when creating domain logic, defining ports, or implementing adapters.
  Configurable: requires project-specific directory paths.
allowed-tools: Read, Write, Grep, Glob
---

# Hexagonal Architecture

> **Configurable Skill**: Edit the Configuration section for your project structure.

## Purpose

Guides implementation of Hexagonal Architecture, ensuring proper separation between
domain logic (inside) and infrastructure (outside).

## Configuration

```yaml
# Required - specify your project's directory structure
paths:
  domain_dir: src/domain              # Where domain entities and logic live
  ports_dir: src/domain/ports         # Where port interfaces are defined
  adapters_dir: src/infrastructure    # Where adapter implementations live
  application_dir: src/application    # Where use cases/services live

# Optional
naming:
  file_case: kebab-case               # kebab-case, PascalCase, or camelCase
  use_barrel_files: true              # Create index.ts exports (default: true)

# Architectural decisions (embedded markdown for complex guidance)
decisions:
  repository_placement: |
    ## Where to Place Repositories

    **Decision:** Repositories can be use-case-specific or shared.

    | Condition | Location | Rationale |
    |-----------|----------|-----------|
    | Used by 1 use case | `{application_dir}/{use-case}/repositories/` | Keep cohesion |
    | Used by 2+ use cases | `{domain_dir}/repositories/` | Avoid duplication |

    **Example:** `OrderRepository` used by CreateOrder and CancelOrder → shared.
```

> **Note on `decisions:`** The `decisions:` key holds embedded markdown using YAML multiline
> strings (`|`). This keeps all configuration in one block while allowing rich decision
> documentation that Claude can render and apply.

## Self-Initialization

If configuration is empty, I will:

1. **Scan** for common patterns:
   - `src/domain/` → domain_dir
   - `core/domain/` → domain_dir
   - `lib/domain/` → domain_dir

2. **Prompt** if not found:
   - "Where should domain logic live? (e.g., src/domain)"

## Instructions

### Creating Domain Entities

1. Create entity in the **domain directory** (`domain_dir`)
2. Entity should have no external dependencies
3. Export from barrel file if `use_barrel_files` is enabled

### Defining Ports

1. Create interface in the **ports directory** (`ports_dir`)
2. Ports define what the domain needs from the outside world
3. Name convention: `I{Noun}Repository`, `I{Noun}Service`

### Implementing Adapters

1. Create implementation in the **adapters directory** (`adapters_dir`)
2. Adapter implements a port interface
3. Contains all infrastructure-specific code (database, HTTP, etc.)

### Creating Use Cases

1. Create use case in the **application directory** (`application_dir`)
2. Use case orchestrates domain entities via ports
3. Inject adapters through constructor

````

---

## Use Cases for Configurable Skills

### Architecture Patterns

- Hexagonal Architecture (ports, adapters, domain)
- Clean Architecture (entities, use cases, interfaces)
- MVC variants (models, views, controllers)

### Testing Conventions

- Test file locations and naming
- Fixture patterns
- Mock/stub conventions

### Coding Standards

- File naming patterns
- Export conventions
- Error handling patterns

### Workflow Automation

- PR templates with project-specific sections
- Deployment scripts with environment paths
- Build configurations

---

## Limitations and Future Work

### Current Limitations

1. **No runtime enforcement**: Configuration is documentation, not code
2. **Manual configuration**: Users must edit the YAML block
3. **No validation**: Skills can't verify configuration is correct

### Future Improvements (Epic 3)

1. **Example implementations**: Concrete skills demonstrating the pattern
2. **Self-initialization logic**: Built-in scan/prompt patterns
3. **Configuration validation**: Check paths exist, types are correct

---

## Summary

Configurable skills solve the portability problem by separating:

- **The Pattern** (universal knowledge) in skill instructions
- **The Configuration** (project-specific values) in a YAML block

**Key Concepts:**

1. **Configuration Schema**: YAML block with required/optional keys
2. **Self-Initialization**: Scan, prompt, or hybrid to get values
3. **Reference by Name**: Instructions use "the domain directory" not `src/domain`

This pattern enables true skill reusability—write once, configure per project.

---

_Previous: [Section 6 - Migration Guide](06-migration-guide.md)_
_Next: [Worked Examples](examples/worked-examples.md)_
