---
name: sedona-implement
description: Implement SedonaDB spatial function wrappers in the sx R package. Use after completing sedona-research to create the R function, documentation, and tests. Requires research document to exist. Use when this capability is needed.
metadata:
  author: e-kotov
---

# SedonaDB Function Implementation Skill

Use this skill to implement a new sx wrapper function AFTER completing research with the `sedona-research` skill.

## Prerequisites

- [ ] Research document exists at `.claude/research/sedona/{function_name}.md`
- [ ] All research checklist items are complete

If research is not complete, STOP and use the `sedona-research` skill first.

## Workflow

### 1. Review research document

Read `.claude/research/sedona/{function_name}.md` and extract:
- SQL signature and argument types
- ArgMatcher types from Rust
- sf equivalent and unsupported parameters
- Edge cases to handle

### 2. Design the R function

#### Naming convention
- Function name: `sx_{snake_case_name}` (e.g., `sx_buffer`, `sx_centroid`)
- File name: `R/sx_{name}.R`

#### Parameter mapping
Map SedonaDB arguments to R parameters:
- Primary geometry: `x`
- Secondary geometry: `y` (for binary operations)
- Numeric parameters: Use descriptive names matching sf (e.g., `dist` for distance)
- Add `...` to catch and warn about unsupported sf arguments
- Add standard sx parameters: `output`, `view_name`, `verbosity`
- Add `use_s2` if function has geography implications

### 3. Implement the function

Create `R/sx_{name}.R` using this template:

```r
#' [Title from SQL Docs]
#'
#' @description
#' [Description from SQL Docs]
#'
#' @param x `r sx_docs("x", role = "[role description]")`
#' @param arg Description from research.
#' @param output `r sx_docs("output")`
#' @param view_name `r sx_docs("view_name")`
#' @param verbosity `r sx_docs("verbosity")`
#' @param use_s2 `r sx_docs("use_s2")`
#' @param ... Ignored. Used to catch and warn about unsupported sf arguments.
#'
#' @return Result (type depends on `output`)
#'
#' @seealso
#' \href{https://sedona.apache.org/latest/api/sql/Function/#st_function}{SedonaDB ST_Function documentation}
#'
#' \code{\link[sf:st_function]{sf::st_function()}}
#'
#' @export
sx_function <- function(
  x,
  arg,
  output = NULL,
  view_name = NULL,
  verbosity = NULL,
  use_s2 = NULL,
  ...
) {
  # Capture ... to check for sf-specific ignored arguments
  dots <- list(...)
  sf_args <- c("unsupported_arg1", "unsupported_arg2")  # From research
  ignored <- intersect(names(dots), sf_args)

  if (length(ignored) > 0) {
    sx_warn(
      "SedonaDB backend ignores sf-specific arguments: {.arg {ignored}}."
    )
  }

  # Capability checks
  if (!is.null(view_name)) {
    sx_require_capability("views", "view_name")
    output <- "view"
  }

  # Handle per-call verbosity override
  if (!is.null(verbosity)) {
    rlang::local_options(sx.verbosity = verbosity)
  }

  # Handle explicit use_s2 override (if applicable)
  if (!is.null(use_s2)) {
    if (!is.logical(use_s2) || length(use_s2) != 1L) {
      cli::cli_abort("{.arg use_s2} must be a logical of length 1 (or NULL).")
    }
    rlang::local_options(sx_temp_use_s2 = use_s2)
  }

  # Prepare inputs using unified funnel
  x_prepared <- sx_prepare_input(x, "x")

  # Setup cleanup
  on.exit(
    {
      if (x_prepared$cleanup) sx_drop_view(x_prepared$view)
    },
    add = TRUE
  )

  x_view <- x_prepared$view
  x_geom <- get_geom_column(x_view)

  # Validate additional arguments (based on ArgMatcher types from research)
  if (!is.numeric(arg) || length(arg) != 1L) {
    cli::cli_abort("{.arg arg} must be a single numeric value.")
  }

  # Build SQL
  sql <- glue::glue(
    "SELECT ST_Function(v1.{x_geom}, {arg}) AS {x_geom}, v1.* EXCLUDE ({x_geom})
     FROM {x_view} v1"
  )

  # Execute query
  res_df <- sx_internal_execute(sql)

  sx_debug("Spatial function applied via SedonaDB.")

  # Handle output
  sx_handle_output(res_df, output, view_name = view_name)
}
```

### 4. Write unit tests

Create `tests/testthat/test-sx_{name}.R`:

```r
test_that("sx_{name} works on planar data", {
  skip_if_no_sedona()

  res <- sx_{name}(nc_5070, arg = value, output = "sf")

  expect_s3_class(res, "sf")
  expect_equal(nrow(res), nrow(nc_5070))
  # Function-specific assertions
})

test_that("sx_{name} handles column name as argument (if applicable)", {
  skip_if_no_sedona()

  # Add test data with column
  test_data <- nc_5070
  test_data$arg_col <- value

  res <- sx_{name}(test_data, arg = "arg_col", output = "sf")

  expect_s3_class(res, "sf")
})

test_that("sx_{name} errors on invalid input", {
  skip_if_no_sedona()

  expect_error(
    sx_{name}(nc_5070, arg = "invalid"),
    "must be"
  )
})

test_that("sx_{name} returns view when requested", {
  skip_if_no_sedona()

  res <- sx_{name}(nc_5070, arg = value, output = "view", view_name = "test_{name}_view")

  expect_true(inherits(res, "sedonadb_dataframe"))

  # Cleanup
  sx_drop_view("test_{name}_view")
})

# Add tests for edge cases from research document
```

### 5. Write sf comparison tests

Create `tests/testthat/test-sx_vs_sf_{name}.R`:

```r
# Cross-verification tests: sx_{name} vs sf::st_{name}
# Ensures sx_{name} produces equivalent results to sf

test_that("sx_{name} matches sf::st_{name} on planar data", {
  skip_if_no_sedona()

  sf_result <- sf::st_{name}(nc_5070, arg = value)
  sx_result <- sx_{name}(nc_5070, arg = value, output = "sf")

  # Compare row counts
  expect_equal(nrow(sx_result), nrow(sf_result))

  # For geometry-returning functions, compare areas or other metrics
  expect_equal(
    as.numeric(sf::st_area(sx_result)),
    as.numeric(sf::st_area(sf_result)),
    tolerance = 0.01  # Account for algorithmic differences
  )
})

test_that("sx_{name} preserves CRS", {
  skip_if_no_sedona()

  result <- sx_{name}(nc_5070, arg = value, output = "sf")

  expect_equal(sf::st_crs(result)$epsg, sf::st_crs(nc_5070)$epsg)
})
```

### 6. Run tests and verify

```bash
# Run specific tests
Rscript -e "devtools::test(filter = 'sx_{name}')"

# Run sf comparison tests
Rscript -e "devtools::test(filter = 'sx_vs_sf_{name}')"

# Redocument
Rscript -e "devtools::document()"

# Format code
air format .
```

## Implementation checklist

- [ ] Research document exists and is complete
- [ ] Created `R/sx_{name}.R` following the template
- [ ] Added `@seealso` link to SedonaDB docs
- [ ] Listed unsupported sf arguments with warnings
- [ ] Created `tests/testthat/test-sx_{name}.R`
- [ ] Created `tests/testthat/test-sx_vs_sf_{name}.R`
- [ ] All tests pass
- [ ] Ran `devtools::document()`
- [ ] Ran `air format .`

## Special cases

### Binary operations (two geometries)

For functions like `ST_Intersection(A, B)`:
- Use `x` and `y` parameters
- Prepare both inputs with `sx_prepare_input()`
- Handle CRS alignment if needed
- Clean up both temporary views

```r
x_prepared <- sx_prepare_input(x, "x")
y_prepared <- sx_prepare_input(y, "y")

on.exit({
  if (x_prepared$cleanup) sx_drop_view(x_prepared$view)
  if (y_prepared$cleanup) sx_drop_view(y_prepared$view)
}, add = TRUE)
```

### Aggregate functions

For functions like `ST_Union_Agg`:
- May need different SQL patterns (GROUP BY)
- Consider returning single geometry vs collection
- Use appropriate window function patterns if needed

### Functions with S2/Geography restrictions

- Check `sx_internal_use_s2()` and `sx_internal_is_geographic()`
- Add appropriate error messages when not supported
- See `R/sx_buffer.R` for example:

```r
if (sx_internal_use_s2() && sx_internal_is_geographic(x_df)) {
  cli::cli_abort(c(
    "sx_{name} is not supported for geographic coordinates when S2 is active.",
    "x" = "SedonaDB does not support ST_{Name} on GEOGRAPHY types.",
    "i" = "Please project your data first or disable S2."
  ))
}
```

### Functions with optional arguments

- Use default values matching sf when possible
- Handle `NULL` values appropriately in SQL
- Document defaults clearly in roxygen

### Column name arguments

Some functions accept either a literal value or a column name:

```r
if (is.numeric(arg) && length(arg) == 1L) {
  arg_val <- sprintf("%f", arg)
} else if (is.character(arg) && length(arg) == 1L) {
  # Assume column name, quote and prefix with alias
  arg_val <- glue::glue('v1."{arg}"')
} else {
  cli::cli_abort(
    "{.arg arg} must be a single numeric value or a column name (character)."
  )
}
```

## Reference implementations

- **Simple unary operation:** `R/sx_buffer.R`, `R/sx_centroid.R`
- **Binary operation:** `R/sx_join.R`
- **Complex operation:** `R/sx_interpolate_aw.R`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/e-kotov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
