# 4-bit Counter RTL-to-GDSII ASIC Flow

Cadence-based RTL-to-GDSII implementation of a 4-bit synchronous counter using the SCL 180 nm flow described in the NIT Durgapur RTLtoGDSII ASIC Design Flow Manual.

## What the project does

The design starts as Verilog RTL, is verified with a testbench, analyzed for coverage, synthesized into a gate-level netlist, and then taken through physical design in Cadence Innovus to generate a final GDSII layout.

## Flow

```text
Verilog RTL
   |
   v
RTL Simulation (Incisive / SimVision)
   |
   v
Code Coverage (IMC)
   |
   v
Logic Synthesis (Genus)
   |
   +--> gate-level netlist
   +--> SDC
   +--> timing / area / power reports
   |
   v
IO / Pad Planning
   |
   v
Floorplanning
   |
   v
Power Planning
   |
   v
Placement
   |
   v
Clock Tree Synthesis
   |
   v
Routing + Post-route Optimization
   |
   v
GDSII Stream Out
   |
   v
Virtuoso GDSII Import / Layout Inspection
```

## Repository structure

- `rtl/` — synthesizable RTL
- `tb/` — RTL testbench
- `synthesis/` — Genus TCL script
- `constraints/` — SDC reference
- `physical_design/` — IO/pad planning and netlist mapping notes
- `innovus/` — Innovus physical-design scripts/placeholders
- `scripts/` — command helpers
- `reports/` — generated reports (keep large generated reports out of Git if desired)
- `docs/` — project documentation

## Important

The PDK and Cadence installation are environment-specific. Do **not** upload proprietary PDK files, standard-cell libraries, LEF/GDS files, or generated proprietary library data to GitHub unless you have permission.

Before running Genus, update the paths in `synthesis/synth_script.tcl`.

The manual uses the SCL 180 nm PDK and demonstrates a 4-bit counter flow.

## RTL behavior

`rst` is active-low and synchronous because it is checked inside `always @(posedge clk)`.

- `rst = 0` -> counter resets to `0000`
- `rst = 1` -> counter increments on every rising clock edge
- Counter wraps from `1111` to `0000`

The supplied testbench uses a 10 ns clock period.

## Main Cadence commands

### RTL simulation

```text
nclaunch -new
```

### Coverage

```text
irun counter.v counter_test.v -access +rwc -coverage all -gui
imc
```

### Synthesis

```text
genus -legacy_ui -f synth_script.tcl
```

### Innovus

```text
innovus
```

### Final GDSII

The final stream-out is performed in Innovus using `streamOut` with the SCL180 technology mapping and library GDS files.

## GitHub note

Commit source RTL, testbench, TCL/SDC scripts, documentation, and reproducibility notes. Avoid committing PDK binaries, Cadence generated databases, temporary simulation directories, and machine-specific absolute paths.
