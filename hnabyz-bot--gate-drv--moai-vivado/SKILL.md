---
name: moai-vivado
description: Xilinx Vivado FPGA development specialist covering RTL simulation with XSim, synthesis, implementation, and bitstream generation. Use when developing FPGA projects, simulating Verilog/SystemVerilog designs, or generating bitstreams for Xilinx devices. Use when this capability is needed.
metadata:
  author: hnabyz-bot
---

# moai-vivado: Xilinx Vivado FPGA Development Specialist

## Quick Reference (30 seconds)

Vivado Development Focus: Command-line driven FPGA development flow with XSim simulation, synthesis, implementation, and bitstream generation for Xilinx FPGA devices.

### Core Workflow Tools

**XSim Simulation Tools** provide fast compilation and simulation without GUI overhead:

- xvlog compiles Verilog and SystemVerilog sources to design object files
- xelab elaborates design into simulation executable with debug capability
- xsim runs simulation with runall or interactive debugging modes

**Vivado Tcl Shell** provides synthesis and implementation through batch mode:

- vivado -mode batch -source script.tcl for non-interactive execution
- launch_runs for synthesis and implementation processes
- write_bitstream for bitstream file generation

### Critical Success Factors

[HARD] Always use `timescale 1ns/1ps` in all RTL modules
WHY: XSim requires explicit timescale for proper simulation timing
IMPACT: Missing timescale causes compilation errors with "Missing time precision" messages

[HARD] Always use absolute paths with xvlog/xelab/xsim commands
WHY: XSim tools do not resolve relative paths correctly on Windows
IMPACT: Relative paths cause "File not found" errors even when files exist

[HARD] Always create dedicated xsim_work directory for simulation artifacts
WHY: Separates compilation outputs from source code and prevents conflicts
IMPACT: Mixing source and output files causes recompilation issues

[HARD] Always use direct tool calls instead of GUI launch_simulation
WHY: GUI wrappers have pipe and encoding issues on Windows
IMPACT: launch_simulation causes "Broken pipe" errors and unreliable simulation

### Anti-Patterns (Failed Approaches)

DO NOT use Vivado GUI launch_simulation command
- Problem: Inter-process pipe failures on Windows
- Solution: Use direct xvlog/xelab/xsim calls

DO NOT use batch scripts with line continuation on Windows
- Problem: Encoding issues with continuation characters
- Solution: Use single-line commands or proper Tcl scripts

DO NOT use relative paths with XSim tools
- Problem: Path resolution failures during compilation
- Solution: Always specify absolute paths starting from drive root

DO NOT skip timescale directives in RTL modules
- Problem: XSim compilation errors
- Solution: Add `timescale 1ns/1ps` as first line in every module

### Quick Decision Guide

Choose XSim command-line flow when:
- Automated testing and CI/CD integration required
- Faster compilation needed without GUI overhead
- Reproducible builds across environments required
- Debug simulation issues with detailed log output needed

Choose Vivado GUI mode when:
- Visual waveform analysis required
- Interactive debugging with breakpoints needed
- IP Integrator block design creation required
- Project-based setup preferred for new designs

---

## Implementation Guide (5 minutes)

### Basic Simulation Setup

Step 1: Prepare RTL sources with required timescale directives

Ensure all Verilog/SystemVerilog modules begin with:

```systemverilog
`timescale 1ns/1ps
module module_name(
    // Module ports and implementation
);
endmodule
```

Step 2: Create dedicated simulation workspace

Execute bash commands to create clean workspace:

```bash
cd vivado
mkdir -p xsim_work && cd xsim_work
```

Step 3: Compile all RTL and testbench sources

Use xvlog with absolute paths and SystemVerilog flag:

```bash
xvlog -sv /absolute/path/rtl/*.sv /absolute/path/tb/*.sv
```

Expected output: Compiled module list with time precision information

Step 4: Elaborate design into simulation executable

Use xelab with debug enabled and snapshot name:

```bash
xelab -debug typical <testbench_name> -s <snapshot_name>
```

Example for testbench named "tb_gate_driver":
```bash
xelab -debug typical tb_gate_driver -s gate_driver_sim
```

Step 5: Run simulation with automatic execution

Use xsim with runall flag to complete simulation:

```bash
xsim <snapshot_name> -runall
```

Complete example:
```bash
xsim gate_driver_sim -runall
```

Expected output: Simulation log with timing information and test results

### Synthesis and Implementation Flow

Step 1: Create Tcl script for synthesis with design sources

Create a file named synth_design.tcl containing:

```tcl
# Read design sources
read_verilog /absolute/path/rtl/*.sv

# Read constraints (optional)
read_xdc /absolute/path/constraints/*.xdc

# Synthesize design targeting specific device
synth_design -top <top_module_name> -part xc7a35tfgg484-1

# Optimize design
opt_design

# Write checkpoint
write_checkpoint -force post_synth.dcp
```

Step 2: Run synthesis in batch mode

Execute Vivado in non-interactive batch mode:

```bash
vivado -mode batch -source synth_design.tcl
```

Expected output: Synthesis metrics including resource utilization and timing

Step 3: Create implementation script

Create a file named impl_design.tcl containing:

```tcl
# Read post-synthesis checkpoint
read_checkpoint post_synth.dcp

# Place design
place_design

# Route design
route_design

# Generate timing report
report_timing_summary -file timing_report.txt

# Write bitstream
write_bitstream -force design.bit
```

Step 4: Run implementation in batch mode

Execute implementation script:

```bash
vivado -mode batch -source impl_design.tcl
```

Expected output: Bitstream file design.bit and timing report

### Troubleshooting Common Issues

**Issue: xvlog reports "Missing time precision" errors**

Root cause: RTL modules missing `timescale directive

Solution: Add `timescale 1ns/1ps` as first line in every module:

```systemverilog
`timescale 1ns/1ps  // Must be first line
module example_module(
    input logic clk,
    output logic data
);
    // Implementation
endmodule
```

**Issue: xelab reports "File not found" for existing sources**

Root cause: Relative paths not resolved by XSim tools

Solution: Use absolute paths starting with drive letter:

```bash
# Wrong (fails):
xvlog -sv ../../rtl/module.sv

# Correct (works):
xvlog -sv D:/workspace/project/rtl/module.sv
```

**Issue: xsim fails with "Broken pipe" error**

Root cause: Using GUI launch_simulation wrapper on Windows

Solution: Use direct tool calls instead:

```bash
# Wrong (GUI wrapper - fails):
vivado -mode gui -tcl "launch_simulation"

# Correct (direct tools - works):
xvlog -sv sources.sv
xelab tb_top -s sim
xsim sim -runall
```

**Issue: Simulation runs but produces no output**

Root cause: Missing $display statements or testbench not running

Solution: Ensure testbench contains:

```systemverilog
initial begin
    $display("Simulation started at time %0t", $time);
    // Test stimulus
    #100;
    $display("Test completed successfully");
    $finish;
end
```

---

## Advanced Patterns (10+ minutes)

For advanced workflows including automated testbench execution, batch synthesis with multiple sources, interactive debugging, CI/CD integration, performance optimization, and complete FPGA flow scripts, see reference.md which contains:

- **Automated Testbench Scripts**: Bash and PowerShell scripts for complete simulation workflow with error checking
- **Batch Synthesis Scripts**: Tcl scripts handling multiple Verilog/SystemVerilog source files with IP Integrator support
- **Interactive Debugging**: GUI-based XSim workflows with breakpoints, waveform analysis, and TCL console commands
- **CI/CD Integration**: GitHub Actions, GitLab CI, and Jenkins pipeline examples for automated testing
- **Performance Optimization**: Parallel compilation, incremental compilation, and synthesis directive exploration
- **Complete Flow Scripts**: End-to-end automation from simulation to bitstream generation

### Quick Advanced Pattern Examples

**Automated Simulation Script** (complete version in reference.md):

```bash
#!/bin/bash
# Key steps: clean, compile, elaborate, run with error checking
xvlog -sv rtl/*.sv tb/*.sv && xelab -debug typical tb_top -s sim && xsim sim -runall
```

**Interactive Debugging** (see reference.md for detailed TCL commands):

```bash
xvlog -sv rtl/*.sv tb/*.sv
xelab -debug all tb_top -s debug_sim
xsim debug_sim -gui  # Launch GUI for waveform analysis
```

**CI/CD Integration** (complete workflows in reference.md):

```yaml
# GitHub Actions example
- xvlog -sv $GITHUB_WORKSPACE/rtl/*.sv $GITHUB_WORKSPACE/tb/*.sv
- xelab -debug typical tb_top -s sim_snapshot
- xsim sim_snapshot -runall
```

---

## Works Well With

- moai-workflow-testing for comprehensive testbench development and verification strategies
- moai-lang-cpp for C++ verification components and testbench integration
- moai-foundation-quality for code quality standards and linting rules
- moai-workflow-ddd for RTL design methodology and architectural patterns

---

## Device-Specific Notes

**Artix-7 xc7a35tfgg484** (Target Device):

- 20,800 logic cells
- 104 DSP slices
- 1,800 Kbits block RAM
- 208 user I/O pins
- Supported in Vivado 2019.1+

**Vivado Version 2024.2**:

- Latest XSim compilation and simulation features
- Improved synthesis runtime and QoR
- Enhanced timing analysis capabilities
- Windows 10/11 and Linux support

---

## Additional Resources

### Extended Documentation

- **reference.md**: Complete synthesis scripts, CI/CD patterns, performance optimization techniques, and advanced debugging workflows
- **examples.md**: Production-ready code examples including counter simulation, gate driver testbench, and anti-pattern demonstrations

### Official Xilinx Documentation

- **UG900 (Vivado Design Suite User Guide: Simulation)**: Complete XSim reference
- **UG901 (Vivado Design Suite User Guide: Synthesis)**: Synthesis optimization techniques
- **UG949 (UltraFast Design Methodology Guide)**: Design best practices
- **Xilinx Forums**: Community support for specific device issues

---

Status: Production Ready
Version: 1.0.0
Updated: 2026-01-23
Target Device: Artix-7 xc7a35tfgg484
Vivado Version: 2024.2

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hnabyz-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
