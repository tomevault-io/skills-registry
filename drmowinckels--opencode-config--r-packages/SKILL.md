---
name: r-package
description: Personal R package development preferences for code style, documentation conventions, and workflow. Complements Posit r-lib skills with specific style choices for minimal comments and neuroimaging package patterns. Use when this capability is needed.
metadata:
  author: drmowinckels
---

# R Package Development - Personal Preferences

Specific style preferences and workflow choices for R package development. Use alongside Posit `testing-r-packages` for comprehensive guidance.

## Code Style Philosophy

### Self-Explanatory Code Without Comments

Functions and variables should be named clearly enough that comments are unnecessary:

```r
# Good: Self-explanatory without comments
calculate_mean_cortical_thickness <- function(surface_data, 
                                               region_labels,
                                               exclude_medial_wall = TRUE) {
  valid_vertices <- identify_valid_vertices(surface_data, exclude_medial_wall)
  regional_means <- compute_regional_means(surface_data, region_labels, valid_vertices)
  regional_means
}

# Bad: Needs comments to explain
calc_mct <- function(sd, rl, emw = TRUE) {
  # Get valid vertices
  vv <- get_vv(sd, emw)
  # Calculate means
  rm <- calc_rm(sd, rl, vv)
  rm
}

# Exception: Comments allowed for workarounds
process_freesurfer_annotation <- function(annot_file) {
  # WORKAROUND: FreeSurfer annot files have non-standard header format
  # that readBin() misinterprets. Skip first 4 bytes manually.
  raw_data <- readBin(annot_file, "raw", n = file.size(annot_file))
  data_without_header <- raw_data[-(1:4)]
  parse_annotation_data(data_without_header)
}
```

**When comments ARE acceptable:**
- Explaining workarounds for upstream bugs
- Documenting non-obvious algorithm choices with citations
- Noting technical constraints (e.g., file format quirks)

### Consistent Naming Patterns

```r
# Good: Consistent verb-noun pattern
read_freesurfer_surface()
read_freesurfer_annotation()
read_freesurfer_curv()

write_freesurfer_surface()
write_freesurfer_annotation()

# Bad: Inconsistent patterns
fs_read_surface()
read_annot_freesurfer()
freesurfer_curv_read()
```

**Naming conventions:**
- Data reading functions: `read_*`
- Data writing functions: `write_*`
- Data transformation: `transform_*`, `convert_*`
- Calculations: `calculate_*`, `compute_*`
- Checks/validation: `is_*`, `has_*`, `validate_*`

## Documentation Preferences

### Minimal but Complete roxygen2

Focus on what users need, not implementation details:

```r
# Good: User-focused documentation
#' Read FreeSurfer surface file
#'
#' Reads surface geometry from FreeSurfer format files. Supports both
#' ASCII and binary formats automatically.
#'
#' @param filepath Path to FreeSurfer surface file
#'
#' @return List with two elements:
#'   * `vertices` - Nx3 matrix of vertex coordinates
#'   * `faces` - Mx3 matrix of face indices (1-indexed)
#'
#' @examples
#' surf <- read_freesurfer_surface("lh.pial")
#' n_vertices <- nrow(surf$vertices)
#'
#' @family freesurfer-io
#' @export
read_freesurfer_surface <- function(filepath) {
  # Implementation
}

# Bad: Over-documented with implementation details
#' Read FreeSurfer surface file
#'
#' This function reads a FreeSurfer surface file by first checking
#' if it's binary or ASCII format, then parsing the header to get
#' the number of vertices and faces, and finally reading the data
#' into R matrices using optimized C++ code via Rcpp.
#'
#' The function performs the following steps:
#' 1. Opens file connection
#' 2. Reads magic number
#' 3. Determines format
#' ...
#' [Users don't need implementation details in documentation]
```

### Example-Driven Documentation

Prioritize realistic examples over abstract descriptions:

```r
# Good: Practical workflow example
#' Calculate cortical thickness statistics
#'
#' @param thickness_file Path to thickness data
#' @param parcellation_file Path to parcellation
#'
#' @examples
#' # Typical neuroimaging workflow
#' thickness <- read_freesurfer_curv("lh.thickness")
#' parcellation <- read_freesurfer_annotation("lh.aparc.annot")
#'
#' # Get mean thickness per region
#' regional_stats <- calculate_regional_thickness(
#'   thickness,
#'   parcellation,
#'   exclude_unknown = TRUE
#' )
#'
#' # Extract specific regions of interest
#' motor_thickness <- regional_stats$thickness[
#'   regional_stats$region == "precentral"
#' ]
```

## Package-Specific Patterns

### Neuroimaging Data Structures

Consistent structure for neuroimaging data:

```r
# Good: Consistent structure across functions
structure(
  list(
    data = numeric_vector,
    metadata = list(
      n_vertices = length(numeric_vector),
      hemisphere = "left",
      structure = "pial"
    )
  ),
  class = c("fs_surface_data", "list")
)

# Bad: Inconsistent attributes
some_data <- numeric_vector
attr(some_data, "n") <- length(numeric_vector)
attr(some_data, "hemi") <- "lh"  # Inconsistent: "left" vs "lh"
```

### Graceful Handling of Missing Data

```r
# Good: Explicit handling with informative messages
read_subject_data <- function(subjects_dir, subject_id, measure) {
  filepath <- file.path(subjects_dir, subject_id, "surf", measure)
  
  if (!file.exists(filepath)) {
    stop(
      "Could not find ", measure, " for subject ", subject_id, "\n",
      "Expected at: ", filepath, "\n",
      "Check that FreeSurfer has been run for this subject.",
      call. = FALSE
    )
  }
  
  read_freesurfer_data(filepath)
}

# Bad: Cryptic errors
read_subject_data <- function(subjects_dir, subject_id, measure) {
  read_freesurfer_data(file.path(subjects_dir, subject_id, "surf", measure))
  # Let file.exists() error propagate with unhelpful message
}
```

## Testing Preferences

### Test Coverage Focus

Prioritize testing of:
1. **Public API** - All exported functions
2. **File I/O** - Reading/writing different formats
3. **Edge cases** - Empty data, missing values, malformed input
4. **Data transformations** - Coordinate conversions, index remapping

De-prioritize testing of:
- Simple getters/setters
- Internal helper functions with trivial logic
- Functions that only call other packages' functions

```r
# Good: Focus on behavior and edge cases
describe("read_freesurfer_surface()", {
  it("reads binary format surfaces", {
    surf <- read_freesurfer_surface(test_path("fixtures/lh.pial"))
    expect_equal(nrow(surf$vertices), 163842)
    expect_equal(ncol(surf$vertices), 3)
  })
  
  it("handles missing files gracefully", {
    expect_error(
      read_freesurfer_surface("nonexistent.file"),
      "Could not find"
    )
  })
  
  it("validates surface structure", {
    surf <- read_freesurfer_surface(test_path("fixtures/lh.pial"))
    expect_true(all(surf$faces > 0))
    expect_true(all(surf$faces <= nrow(surf$vertices)))
  })
})

# Less important: Testing trivial functions
test_that("get_n_vertices returns vertex count", {
  surf <- list(vertices = matrix(1:9, ncol = 3))
  expect_equal(get_n_vertices(surf), 3)
  # This is too simple to need testing
})
```

## Workflow Integration

### Development Cycle

```r
# Typical development workflow
devtools::load_all()              # Load package
devtools::document()              # Update documentation
devtools::test()                  # Run tests
devtools::check()                 # R CMD check

# Before committing
styler::style_pkg()               # Format code
lintr::lint_package()             # Check style
covr::package_coverage()          # Check coverage
```

### Pre-CRAN Checklist

Beyond standard checks, verify:

```r
# Check that examples run
devtools::run_examples()

# Verify vignettes build
devtools::build_vignettes()

# Test on multiple platforms
devtools::check_win_devel()
devtools::check_mac_release()

# Spell check
spelling::spell_check_package()

# Ensure URLs work
urlchecker::url_check()

# Check reverse dependencies if updating existing package
revdepcheck::revdep_check()
```

## Works Well With

- `testing-r-packages` (Posit) - Comprehensive testthat 3+ testing guide (use this for all testing patterns)
- `release-post` (Posit) - Create release announcements
- `brand-yml` (Posit) - pkgdown site branding

## When to Use Me

Use this skill when:
- Setting up a new R package and want style guidance
- Need preferences for code organization and naming
- Working with neuroimaging or FreeSurfer data packages
- Want to understand the "no comments" philosophy

**Do NOT use for:**
- Testing patterns (use Posit `testing-r-packages` instead)
- General R package structure (covered in Posit skills)
- CRAN submission procedures (standard across packages)

## Quick Reference

**Code style:** Self-explanatory names, no comments except workarounds

**Naming:** `verb_noun()` pattern, consistent prefixes

**Documentation:** User-focused, example-driven

**Testing focus:** Public API, file I/O, edge cases

**Workflow:** `load_all()` → `document()` → `test()` → `check()`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/drmowinckels) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
