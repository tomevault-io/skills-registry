---
name: 42-readme-generator
description: Generate comprehensive README files for 42 School projects. Use when creating or improving project documentation. Use when this capability is needed.
metadata:
  author: monedales
---

# 42 README Generator

Generate well-structured README files for 42 School projects.

## README Structure

Every 42 project README should include:

1. **Title & Tagline** (with emojis)
2. **Description** - Overview, problem, key challenge
3. **Technologies & Concepts** - What's used and learned
4. **How It Works** - Visual explanations with ASCII diagrams
5. **Architecture** - Data structures, modules
6. **Source Code Structure** - Directory tree
7. **Instructions** - Build, run, test
8. **Technical Challenges** - Problems and solutions (collapsible)
9. **Useful Links** - Guides and resources
10. **License** - Academic disclaimer

## Title Section Template

```markdown
# project-name 🎯 [emoji]

One-sentence description of what the project does and its educational purpose.
```

## Description Template

```markdown
## Description 📜

[2-3 paragraph overview of the project]

**Key Challenge:** [Main technical difficulty in one sentence]
```

## How It Works Section

Use ASCII diagrams:
```markdown
## How It Works 🔄

### Data Flow:
```
┌─────────┐         ┌─────────┐
│ INPUT   │────────►│ PROCESS │
└─────────┘         └────┬────┘
                         │
                         ▼
                    ┌─────────┐
                    │ OUTPUT  │
                    └─────────┘
\```
```

## Architecture Section

Show actual code structures:
```markdown
## Architecture 🏗️

### Data Structures:
\```c
typedef struct s_data
{
    int     value;
    char    *str;
}   t_data;
\```
```

## Source Structure Template

```markdown
## Source Code Structure 📂

\```
project/
├── binary_name
├── Makefile
├── include/
│   └── header.h
├── src/
│   ├── main.c
│   └── utils.c
└── guides/
    └── guide.md
\```
```

## Instructions Template

```markdown
## Instructions ⚙️

### Building the Project
\```bash
make              # Compile
\```

### Running the Program
\```bash
./program [args]
\```

### Examples:
\```bash
# Basic test
./program test

# Edge case
./program 0
\```

### Development Commands
- Clean: `make clean`
- Full clean: `make fclean`
- Rebuild: `make re`
- Check style: `norminette -R CheckForbiddenSourceHeader`
```

## Technical Challenges Template

```markdown
## Technical Challenges & Solutions 🧩

<details>
<summary><strong>Challenge Name</strong></summary>

**Problem:** Description of the issue

**Solution:** How it was solved

</details>
```

## Style Guidelines

### Emojis
- Title: 1-2 relevant emojis
- Sections: One per header (📜 🛠️ 🔄 🏗️ 📂 ⚙️ 🧩 🔗)
- Body: Minimal use

### Code Blocks
- Always specify language: \```c, \```bash, \```makefile
- Show actual project code when possible

### ASCII Diagrams
- Use: ┌─┐│└┘├┤ for boxes
- Arrows: → ← ↑ ↓ ◄ ►
- Keep simple and readable

## Example Links Section

```markdown
## Useful Links 🔗

### Project Guides (in `/guides/`):
- `guide.md` ⭐ - Main guide
- `concepts.md` - Core concepts

### External Resources:

#### Category 1:
- [Resource Name](URL)

#### Category 2:
- [Resource Name](URL)
```

## 42-Specific Elements

### Norminette
Always include:
```bash
norminette -R CheckForbiddenSourceHeader src/*.c include/*.h
```

### Makefile Rules
Document:
- `make` / `make all`
- `make clean`
- `make fclean`
- `make re`
- `make bonus` (if applicable)

### Testing
Include practical tests:
```bash
# Thread Sanitizer
cc -fsanitize=thread -pthread src/*.c -o program

# Valgrind
valgrind --leak-check=full ./program
```

## Process

1. Read all source files and Makefile
2. Identify key data structures and functions
3. Understand program flow and architecture
4. Create ASCII diagrams for complex processes
5. Write clear, concise descriptions
6. Include practical, tested examples
7. Link to existing guides/ files
8. Add relevant external resources

## Quality Checklist

- [ ] All 10 sections present
- [ ] Compilation commands tested
- [ ] Examples are accurate
- [ ] ASCII diagrams render correctly
- [ ] Code snippets from actual files
- [ ] Links are valid
- [ ] Consistent emoji use
- [ ] Professional tone
- [ ] Markdown properly formatted

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/monedales) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
