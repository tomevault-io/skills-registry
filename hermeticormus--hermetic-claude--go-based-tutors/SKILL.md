---
name: go-based-tutors
description: Pattern for building interactive CLI tutors in Go with standardized colors, progress tracking, and pedagogical elements Use when this capability is needed.
metadata:
  author: hermeticormus
---

# Go-Based Tutors Concoction

Pattern for creating interactive command-line educational tools in Go.

## Core Components

### 1. Standardized Color System

```go
const (
    reset   = "\033[0m"
    bold    = "\033[1m"
    dim     = "\033[2m"
    cRed    = "\033[31m"
    cGreen  = "\033[32m"
    cYellow = "\033[33m"
    cBlue   = "\033[34m"
    cMagenta = "\033[35m"
    cCyan   = "\033[36m"
    cBrightGreen  = "\033[92m"
)

// Semantic helpers
func keyTerm(s string) string { return cYellow + bold + s + reset }  // Important concepts
func do(s string) string      { return cGreen + "DO: " + reset + s } // Best practice
func dont(s string) string    { return cRed + "DON'T: " + reset + s } // Anti-pattern
func tip(s string) string     { return cCyan + "TIP: " + reset + s }  // Helpful hint
func cmd(s string) string     { return cBlue + s + reset }            // Commands/code
func correct(s string) string { return cBrightGreen + bold + s + reset }
func wrong(s string) string   { return cRed + s + reset }
```

### 2. Interactive Elements

Three types of comprehension checks (keep simple for beginners):

```go
// Predict Output - user guesses what code prints
func predictOutput(code, correctOutput, explanation string) bool

// Fill in the Blank - user completes code
func fillBlank(instruction, code, answer, explanation string) bool

// Fix the Bug - multiple choice bug identification
func fixBug(code string, options []string, correctIdx int, explanation string) bool
```

**Key principle**: Questions should be comprehension checks, not knowledge tests. Answers should be obvious from text just read.

### 3. Progress Tracking

```go
type Progress struct {
    CompletedLessons    []int          `json:"completed_lessons"`
    QuizScores          map[string]int `json:"quiz_scores"`
    InteractiveAttempts int            `json:"interactive_attempts"`
    InteractiveCorrect  int            `json:"interactive_correct"`
    LastAccess          time.Time      `json:"last_access"`
}
```

Persist to `progress.json` in working directory.

### 4. Lesson Structure

```go
type Lesson struct {
    ID      int
    Title   string
    Content func()  // Dynamic content with colors & interactions
    Quiz    []QuizQuestion
}
```

### 5. Menu Patterns

- Cyan numbers for options
- Yellow for headers
- Magenta for section dividers
- Dim for "Back" options
- Green checkmarks for completed items

## Existing Implementations

| Tutor | Location | Purpose |
|-------|----------|---------|
| GoTutor | `~/projects/08-DEVELOPMENT/learning/boot.dev/projects/learn-go-pocket-projects/14-gotutor/` | Go language fundamentals |
| LinuxTutor | `~/projects/08-DEVELOPMENT/learning/boot.dev/projects/learn-go-pocket-projects/15-linuxtutor/` | Linux/LPI certification prep |
| VibeReader | `~/projects/08-DEVELOPMENT/learning/vibe-engineering-tutor/` | Interactive book reading (PDF→sections)

## Creating New Tutors

1. Copy color constants and helpers from existing tutor
2. Define lesson structure with `Content func()` for dynamic rendering
3. Add interactive checkpoints within lessons (simple comprehension)
4. Implement progress persistence
5. Follow menu color conventions for consistency

## Interactive Book Reader Pattern (Gen-Z Boring-Proof)

For converting PDFs/books into interactive reading:

### 1. Extract & Split
```bash
pdftotext "book.pdf" "book.txt"
# Split by chapter markers
```

### 2. Section Structure
```go
type Section struct {
    ID           string
    Title        string
    Content      string
    KeyConcept   string   // Main takeaway user must identify
    Alternatives []string // Acceptable variations
    Hint         string   // Help if struggling
}
```

### 3. Key Concept Verification
- User must type the key concept to proceed
- Fuzzy matching (lowercase, ignore hyphens)
- Multiple alternatives accepted
- Hints available, skip option for emergencies
- Tracks first-try accuracy

### 4. Anti-Boring Features
- Can't just press Enter - must demonstrate comprehension
- Progress bars and completion tracking
- First-try accuracy gamification

## Design Principles

- **Simplicity**: Interactive questions confirm reading, not test knowledge
- **Consistency**: Same colors across all tutors
- **Progress**: Visual feedback on completion status
- **Encouragement**: Positive feedback, helpful hints on wrong answers
- **Engagement**: Require input to proceed (no passive reading)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermeticormus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
