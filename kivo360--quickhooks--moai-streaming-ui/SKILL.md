---
name: moai-streaming-ui
description: Enhanced streaming UI system with progress indicators, status displays, and interactive feedback mechanisms. Use when running long-running operations, displaying progress, providing user feedback, or when visual indicators enhance user experience during complex workflows. Use when this capability is needed.
metadata:
  author: kivo360
---

# Enhanced Streaming UI System

## Skill Metadata

| Field | Value |
| ----- | ----- |
| Version | 1.0.0 |
| Tier | Alfred (User Experience) |
| Auto-load | During long-running operations |
| Purpose | Enhanced visual feedback and progress indication |

---

## What It Does

Advanced streaming UI system that provides rich visual feedback, progress indicators, and interactive status displays during complex operations. Enhances user experience by making long-running processes transparent and engaging.

**Core capabilities**:
- ✅ Multi-style progress indicators (bars, spinners, percentages)
- ✅ Real-time status updates and progress tracking
- ✅ Interactive user feedback mechanisms
- ✅ Color-coded severity levels and alerts
- ✅ Step-by-step workflow visualization
- ✅ Performance metrics and timing information
- ✅ Error state handling and recovery guidance
- ✅ Completion summaries and next step suggestions

---

## When to Use

- ✅ During long-running operations (>5 seconds)
- ✅ When executing multi-step workflows
- ✅ During file operations or API calls
- ✅ When user needs progress feedback
- ✅ During background processing tasks
- ✅ When running tests or builds
- ✅ During complex Alfred command executions

---

## UI Components Library

### 1. Progress Indicators

#### Progress Bars
```python
# Basic progress bar
progress_bar(65, 100, "Installing dependencies")
# Output: [████████████████████                    ] 65%

# Animated progress bar
animated_progress(current_step, total_steps, steps)
# Output: Step 3/5: [⚙️] Configuring database...
```

#### Spinners
```python
# Basic spinner
spinner("Processing files", "dots")
# Output: Processing files...

# Custom spinner
spinner("Downloading", "arrow")
# Output: Downloading ↗
```

#### Percentage Display
```python
# Percentage with context
percentage_with_context(45, "Code compilation")
# Output: 📊 Code compilation: 45% complete
```

### 2. Status Indicators

#### Success States
```python
success_indicator("Build completed successfully")
# Output: ✅ Build completed successfully

success_with_details("Tests passed", "127/128 tests passed")
# Output: ✅ Tests passed (127/128)
```

#### Warning States
```python
warning_indicator("Memory usage high")
# Output: ⚠️ Memory usage high: 85% used

warning_with_action("Disk space low", "Clear cache files")
# Output: ⚠️ Disk space low → Action: Clear cache files
```

#### Error States
```python
error_indicator("Connection failed")
# Output: ❌ Connection failed

error_with_recovery("API timeout", "Retry with exponential backoff")
# Output: ❌ API timeout → Recovery: Retry with exponential backoff
```

### 3. Interactive Elements

#### User Prompts
```python
user_prompt("Continue with deployment?", ["yes", "no", "cancel"])
# Output: ❓ Continue with deployment? [Y]es [N]o [C]ancel

user_prompt_with_default("Choose environment", ["dev", "staging", "prod"], "staging")
# Output: 🌍 Choose environment [D]ev [S]taging [P]rod (default: staging)
```

#### Confirmation Required
```python
confirmation_required("Delete 15 files?")
# Output: ⚠️ Confirmation required: Delete 15 files? [y/N]

confirmation_with_details("Merge branch", "main → feature/auth")
# Output: 🔄 Confirm merge: main → feature/auth? [y/N]
```

### 4. Workflow Visualization

#### Step Indicators
```python
workflow_steps([
    ("✅", "Setup completed"),
    ("🔄", "Running tests..."),
    ("⏸️", "Build pending"),
    ("⏸️", "Deploy pending")
])
# Output:
# Step 1: ✅ Setup completed
# Step 2: 🔄 Running tests...
# Step 3: ⏸️ Build pending
# Step 4: ⏸️ Deploy pending
```

#### Phase Indicators
```python
phase_indicator("Phase 2/4: Implementation", 25)
# Output: 🎯 Phase 2/4: Implementation [█████                        ] 25%
```

---

## Integration Patterns

### 1. Alfred Command Integration
```python
# In /alfred:2-run implementation
def run_spec_implementation(spec_id):
    # Start progress tracking
    Skill("moai-streaming-ui")
    # → Display: 🚀 Starting SPEC-AUTH-001 implementation...

    for step in implementation_steps:
        # Show current step
        show_step_progress(step)
        # → Display: 🔄 Step 2/5: Writing tests...

        # Execute step
        result = execute_step(step)

        # Update status
        if result.success:
            success_step(step)
            # → Display: ✅ Step 2 completed: Tests written
        else:
            error_step(step, result.error)
            # → Display: ❌ Step 2 failed: Test syntax error
```

### 2. Long-Running Operation Wrapper
```python
def with_progress_indicator(operation, description):
    """Wrap any operation with progress UI"""
    Skill("moai-streaming-ui")

    # Start indicator
    start_progress(description)

    try:
        result = operation()

        # Success completion
        complete_progress(description, success=True)
        return result

    except Exception as e:
        # Error completion
        complete_progress(description, success=False, error=str(e))
        raise
```

### 3. Background Task Monitoring
```python
def monitor_background_task(task_id):
    """Monitor and display background task progress"""
    while not task_complete(task_id):
        progress = get_task_progress(task_id)

        Skill("moai-streaming-ui")
        show_progress(progress.percentage, progress.current_step)

        sleep(1)  # Update every second

    # Final status
    Skill("moai-streaming-ui")
    show_completion(task_result)
```

---

## Progress Tracking Systems

### 1. Multi-Step Workflows
```python
class WorkflowProgress:
    def __init__(self, steps):
        self.steps = steps
        self.current = 0

    def start(self):
        Skill("moai-streaming-ui")
        show_workflow_start(self.steps)

    def next_step(self):
        self.current += 1
        Skill("moai-streaming-ui")
        show_current_step(self.current, self.steps[self.current])

    def complete(self):
        Skill("moai-streaming-ui")
        show_workflow_complete()

# Usage
workflow = WorkflowProgress([
    "Analyze requirements",
    "Write tests",
    "Implement code",
    "Run validation",
    "Generate documentation"
])

workflow.start()
# → 🎯 Starting workflow: 5 steps planned
```

### 2. File Operations Progress
```python
def track_file_operation(files, operation):
    """Track progress for file operations"""
    total = len(files)

    for i, file_path in enumerate(files):
        current_progress = (i + 1) / total * 100

        Skill("moai-streaming-ui")
        show_file_progress(operation, file_path, current_progress, i + 1, total)

        # Perform operation
        perform_operation(file_path)

    # Completion
    Skill("moai-streaming-ui")
    show_operation_complete(operation, total)

# Usage
track_file_operation(source_files, "Copying files")
# → 📁 Copying files: file.py (45/100) [███████████████                              ] 45%
```

### 3. API/Network Operations
```python
def track_api_requests(requests):
    """Track progress for multiple API requests"""
    completed = 0
    total = len(requests)

    for request in requests:
        Skill("moai-streaming-ui")
        show_api_progress("Making API request", completed + 1, total)

        try:
            response = make_request(request)
            completed += 1

            Skill("moai-streaming-ui")
            show_api_success(completed, total)

        except Exception as e:
            Skill("moai-streaming-ui")
            show_api_error(str(e), completed + 1, total)
```

---

## Status Display Templates

### 1. Operation Start
```python
def show_operation_start(operation, details=None):
    """Display operation start message"""
    lines = [f"🚀 {operation}"]

    if details:
        lines.append(f"📋 {details}")

    lines.append("⏱️  Started at " + datetime.now().strftime("%H:%M:%S"))

    Skill("moai-streaming-ui")
    display_message("\n".join(lines))
```

### 2. Progress Update
```python
def show_progress_update(operation, progress, current_item=None):
    """Display progress update"""
    percentage = progress.percentage
    bar = create_progress_bar(percentage)

    lines = [
        f"🔄 {operation}",
        f"📊 {bar} {percentage:.1f}%",
    ]

    if current_item:
        lines.append(f"📁 Current: {current_item}")

    if progress.eta:
        lines.append(f"⏱️  ETA: {progress.eta}")

    Skill("moai-streaming-ui")
    display_message("\n".join(lines))
```

### 3. Operation Completion
```python
def show_operation_complete(operation, result, duration=None):
    """Display operation completion"""
    if result.success:
        lines = [
            f"✅ {operation} completed successfully"
        ]

        if result.summary:
            lines.append(f"📊 {result.summary}")

    else:
        lines = [
            f"❌ {operation} failed"
        ]

        if result.error:
            lines.append(f"🔴 Error: {result.error}")

    if duration:
        lines.append(f"⏱️  Duration: {duration}")

    lines.append(f"🏁 Finished at " + datetime.now().strftime("%H:%M:%S"))

    Skill("moai-streaming-ui")
    display_message("\n".join(lines))
```

---

## Error Handling and Recovery

### 1. Retry Mechanisms
```python
def show_retry_attempt(operation, attempt, max_attempts):
    """Display retry attempt information"""
    Skill("moai-streaming-ui")

    message = f"🔄 {operation} - Retry {attempt}/{max_attempts}"
    if attempt > 1:
        message += f" (previous attempts failed)"

    display_message(message)
```

### 2. Error Recovery Options
```python
def show_recovery_options(error, options):
    """Display error recovery options to user"""
    Skill("moai-streaming-ui")

    lines = [
        f"❌ Error: {error}",
        "🔧 Recovery options:"
    ]

    for i, option in enumerate(options, 1):
        lines.append(f"   {i}. {option}")

    lines.append("❓ Choose recovery option [1-{}]".format(len(options)))

    display_message("\n".join(lines))
```

### 3. Graceful Degradation
```python
def show_fallback_behavior(operation, fallback_reason):
    """Display fallback behavior information"""
    Skill("moai-streaming-ui")

    message = f"⚠️ {operation} - Using fallback: {fallback_reason}"
    display_message(message)
```

---

## Performance Considerations

### 1. Update Frequency
```python
# Don't update too frequently (avoid spam)
MIN_UPDATE_INTERVAL = 0.5  # 500ms minimum

# Throttle updates
if time_since_last_update() < MIN_UPDATE_INTERVAL:
    return  # Skip this update
```

### 2. Memory Efficiency
```python
# Reuse UI elements
progress_bar_template = "[{█}{ }{ }]"
status_cache = {}

# Limit displayed items
MAX_DISPLAY_ITEMS = 50
if len(items) > MAX_DISPLAY_ITEMS:
    items = items[:MAX_DISPLAY_ITEMS] + [f"... and {len(items) - MAX_DISPLAY_ITEMS} more"]
```

### 3. Async Updates
```python
# Non-blocking UI updates
async def update_progress_async(progress):
    """Update UI without blocking operation"""
    Skill("moai-streaming-ui")
    show_progress(progress)
    await asyncio.sleep(0)  # Yield control
```

---

## Usage Examples

### Example 1: Test Execution
```python
def run_tests_with_progress():
    """Run tests with progress indication"""
    Skill("moai-streaming-ui")
    show_operation_start("Running test suite", "128 tests found")

    tests = get_all_tests()
    passed = 0
    failed = 0

    for i, test in enumerate(tests):
        progress = (i + 1) / len(tests) * 100

        Skill("moai-streaming-ui")
        show_progress_update("Running tests", progress, test.name)

        result = run_single_test(test)

        if result.passed:
            passed += 1
        else:
            failed += 1
            Skill("moai-streaming-ui")
            show_test_failure(test.name, result.error)

    # Final summary
    Skill("moai-streaming-ui")
    show_operation_complete("Test suite", TestResult(passed, failed))
```

### Example 2: File Processing
```python
def process_files_with_progress(files, operation):
    """Process files with progress tracking"""
    Skill("moai-streaming-ui")
    show_operation_start(operation, f"{len(files)} files to process")

    for i, file_path in enumerate(files):
        progress = (i + 1) / len(files) * 100

        Skill("moai-streaming-ui")
        show_file_progress(operation, file_path.name, progress, i + 1, len(files))

        try:
            process_file(file_path)
            Skill("moai-streaming-ui")
            show_file_success(file_path.name)
        except Exception as e:
            Skill("moai-streaming-ui")
            show_file_error(file_path.name, str(e))
```

### Example 3: API Workflow
```python
def execute_api_workflow_with_progress():
    """Execute multi-step API workflow with visual feedback"""
    steps = [
        ("Authenticate", authenticate_api),
        ("Upload data", upload_data),
        ("Process results", process_results),
        ("Download response", download_response)
    ]

    Skill("moai-streaming-ui")
    show_workflow_start([step[0] for step in steps])

    for i, (step_name, step_func) in enumerate(steps):
        Skill("moai-streaming-ui")
        show_current_step(i + 1, len(steps), step_name)

        try:
            result = step_func()
            Skill("moai-streaming-ui")
            show_step_success(step_name)
        except Exception as e:
            Skill("moai-streaming-ui")
            show_step_error(step_name, str(e))
            raise
```

---

**End of Skill** | Rich visual feedback for enhanced user experience during complex operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kivo360) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
