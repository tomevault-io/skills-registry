---
name: imagemagick
description: > Use when this capability is needed.
metadata:
  author: jaimestill
---

# ImageMagick Integration

## When This Skill Applies

- Converting documents to images
- Processing or transforming images
- Rendering PDFs to images
- Using ImageMagick from Go code

## Principles

### 1. ImageMagick Integration Pattern

```go
// Configuration for ImageMagick operations
type ImageConfig struct {
    Format     string         // Output format: "png", "jpg", "webp"
    DPI        int            // Resolution (default: 150)
    Quality    int            // JPEG quality (1-100)
    Background string         // Background color
    Options    map[string]any // Additional options
}

// Renderer interface
type Renderer interface {
    Render(ctx context.Context, input []byte) ([]byte, error)
}

// ImageMagick implementation
type imageMagickRenderer struct {
    cfg ImageConfig
}

func NewImageMagickRenderer(cfg ImageConfig) (Renderer, error) {
    // Verify ImageMagick is available
    if _, err := exec.LookPath("magick"); err != nil {
        return nil, fmt.Errorf("ImageMagick not installed: %w", err)
    }

    // Apply defaults
    if cfg.DPI == 0 {
        cfg.DPI = 150
    }
    if cfg.Format == "" {
        cfg.Format = "png"
    }
    if cfg.Quality == 0 {
        cfg.Quality = 90
    }

    return &imageMagickRenderer{cfg: cfg}, nil
}
```

### 2. Command Building

Build ImageMagick commands safely and consistently.

```go
func (r *imageMagickRenderer) buildArgs(inputPath, outputPath string) []string {
    args := []string{
        "-density", strconv.Itoa(r.cfg.DPI),
    }

    // Background handling
    if r.cfg.Background != "" {
        args = append(args, "-background", r.cfg.Background, "-flatten")
    }

    // Format-specific options
    switch r.cfg.Format {
    case "jpg", "jpeg":
        args = append(args, "-quality", strconv.Itoa(r.cfg.Quality))
    case "png":
        args = append(args, "-define", "png:compression-level=9")
    case "webp":
        args = append(args, "-quality", strconv.Itoa(r.cfg.Quality))
    }

    // Apply custom options
    for key, value := range r.cfg.Options {
        args = append(args, "-"+key, fmt.Sprint(value))
    }

    // Input and output
    args = append(args, inputPath, outputPath)

    return args
}

func (r *imageMagickRenderer) Render(ctx context.Context, input []byte) ([]byte, error) {
    // Create temp directory
    tmpDir, err := os.MkdirTemp("", "imagemagick-*")
    if err != nil {
        return nil, fmt.Errorf("create temp dir: %w", err)
    }
    defer os.RemoveAll(tmpDir)

    // Write input
    inputPath := filepath.Join(tmpDir, "input")
    if err := os.WriteFile(inputPath, input, 0644); err != nil {
        return nil, fmt.Errorf("write input: %w", err)
    }

    // Build output path
    outputPath := filepath.Join(tmpDir, "output."+r.cfg.Format)

    // Build and execute command
    args := r.buildArgs(inputPath, outputPath)
    cmd := exec.CommandContext(ctx, "magick", args...)

    output, err := cmd.CombinedOutput()
    if err != nil {
        return nil, fmt.Errorf("magick failed: %w\ncommand: magick %s\noutput: %s",
            err, strings.Join(args, " "), output)
    }

    // Read result
    result, err := os.ReadFile(outputPath)
    if err != nil {
        return nil, fmt.Errorf("read output: %w", err)
    }

    return result, nil
}
```

### 3. Temporary File Handling

Always clean up temporary files, even on error.

```go
func ProcessImage(ctx context.Context, input []byte, operations []Operation) ([]byte, error) {
    // Create isolated temp directory
    tmpDir, err := os.MkdirTemp("", "img-process-*")
    if err != nil {
        return nil, fmt.Errorf("create temp dir: %w", err)
    }
    defer os.RemoveAll(tmpDir)  // Always cleanup

    inputPath := filepath.Join(tmpDir, "input.png")
    outputPath := filepath.Join(tmpDir, "output.png")

    if err := os.WriteFile(inputPath, input, 0644); err != nil {
        return nil, fmt.Errorf("write input: %w", err)
    }

    // Chain operations
    currentInput := inputPath
    for i, op := range operations {
        currentOutput := filepath.Join(tmpDir, fmt.Sprintf("step-%d.png", i))

        if err := op.Execute(ctx, currentInput, currentOutput); err != nil {
            return nil, fmt.Errorf("operation %d failed: %w", i, err)
        }

        currentInput = currentOutput
    }

    // Final output
    return os.ReadFile(currentInput)
}
```

### 4. Error Message Formatting

Include command details in error messages for debugging.

```go
func runMagick(ctx context.Context, args []string) error {
    cmd := exec.CommandContext(ctx, "magick", args...)

    output, err := cmd.CombinedOutput()
    if err != nil {
        // Include full context for debugging
        return &MagickError{
            Command: "magick " + strings.Join(args, " "),
            Output:  string(output),
            Err:     err,
        }
    }
    return nil
}

type MagickError struct {
    Command string
    Output  string
    Err     error
}

func (e *MagickError) Error() string {
    return fmt.Sprintf("ImageMagick failed\nCommand: %s\nOutput: %s\nError: %v",
        e.Command, e.Output, e.Err)
}

func (e *MagickError) Unwrap() error {
    return e.Err
}
```

## Patterns

### PDF to Image Conversion

```go
func PDFToImages(ctx context.Context, pdfData []byte, dpi int) ([][]byte, error) {
    tmpDir, err := os.MkdirTemp("", "pdf-convert-*")
    if err != nil {
        return nil, err
    }
    defer os.RemoveAll(tmpDir)

    pdfPath := filepath.Join(tmpDir, "input.pdf")
    if err := os.WriteFile(pdfPath, pdfData, 0644); err != nil {
        return nil, err
    }

    // Output pattern for multi-page
    outputPattern := filepath.Join(tmpDir, "page-%04d.png")

    cmd := exec.CommandContext(ctx, "magick",
        "-density", strconv.Itoa(dpi),
        pdfPath,
        "-quality", "90",
        outputPattern,
    )

    if output, err := cmd.CombinedOutput(); err != nil {
        return nil, fmt.Errorf("conversion failed: %w\n%s", err, output)
    }

    // Collect output files
    files, err := filepath.Glob(filepath.Join(tmpDir, "page-*.png"))
    if err != nil {
        return nil, err
    }

    sort.Strings(files)

    var pages [][]byte
    for _, f := range files {
        data, err := os.ReadFile(f)
        if err != nil {
            return nil, err
        }
        pages = append(pages, data)
    }

    return pages, nil
}
```

### Image Resize

```go
func Resize(ctx context.Context, input []byte, width, height int) ([]byte, error) {
    tmpDir, err := os.MkdirTemp("", "resize-*")
    if err != nil {
        return nil, err
    }
    defer os.RemoveAll(tmpDir)

    inputPath := filepath.Join(tmpDir, "input")
    outputPath := filepath.Join(tmpDir, "output.png")

    if err := os.WriteFile(inputPath, input, 0644); err != nil {
        return nil, err
    }

    geometry := fmt.Sprintf("%dx%d", width, height)

    cmd := exec.CommandContext(ctx, "magick",
        inputPath,
        "-resize", geometry,
        "-strip",  // Remove metadata
        outputPath,
    )

    if output, err := cmd.CombinedOutput(); err != nil {
        return nil, fmt.Errorf("resize failed: %w\n%s", err, output)
    }

    return os.ReadFile(outputPath)
}
```

### Format Conversion

```go
func Convert(ctx context.Context, input []byte, format string, quality int) ([]byte, error) {
    tmpDir, err := os.MkdirTemp("", "convert-*")
    if err != nil {
        return nil, err
    }
    defer os.RemoveAll(tmpDir)

    inputPath := filepath.Join(tmpDir, "input")
    outputPath := filepath.Join(tmpDir, "output."+format)

    if err := os.WriteFile(inputPath, input, 0644); err != nil {
        return nil, err
    }

    args := []string{inputPath}

    if quality > 0 && (format == "jpg" || format == "jpeg" || format == "webp") {
        args = append(args, "-quality", strconv.Itoa(quality))
    }

    args = append(args, outputPath)

    cmd := exec.CommandContext(ctx, "magick", args...)

    if output, err := cmd.CombinedOutput(); err != nil {
        return nil, fmt.Errorf("convert failed: %w\n%s", err, output)
    }

    return os.ReadFile(outputPath)
}
```

## Anti-Patterns

### Using Deprecated Commands

```go
// Bad: Old ImageMagick command
cmd := exec.Command("convert", args...)
```

```go
// Good: Modern ImageMagick 7+ command
cmd := exec.Command("magick", args...)
```

### Insecure Input Handling

```go
// Bad: User input in command
cmd := exec.Command("magick", userProvidedPath, outputPath)
```

```go
// Good: Validate and sanitize paths
if !filepath.IsLocal(userPath) {
    return errors.New("invalid path")
}
safePath := filepath.Clean(filepath.Join(baseDir, userPath))
cmd := exec.Command("magick", safePath, outputPath)
```

### Missing Context Cancellation

```go
// Bad: No timeout or cancellation
cmd := exec.Command("magick", args...)
cmd.Run()
```

```go
// Good: Use context for timeout
ctx, cancel := context.WithTimeout(ctx, 30*time.Second)
defer cancel()
cmd := exec.CommandContext(ctx, "magick", args...)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaimestill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
