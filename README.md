# x86 Validation Tool

A Python tool for validating x86-64 instruction execution through **differential testing**:
random instruction sequences are executed both in the [Unicorn](https://www.unicorn-engine.org/) CPU emulator
and natively on the host CPU, and the resulting register and flag states are compared.

> 🚧 **Status: in progress.** See the [roadmap](#roadmap) below.

## How it works

```
                        ┌──► Unicorn emulator (reference model) ──┐
random x86-64 code  ────┤                                         ├──► compare registers + flags ──► mismatch report
                        └──► host CPU (native execution) ─────────┘
```

1. **Generate** a random sequence of x86-64 instructions and random initial register values.
2. **Assemble** the instructions into machine code.
3. **Execute** the same machine code twice: in the Unicorn emulator and natively on the host CPU.
4. **Compare** the final general-purpose registers and arithmetic flags (CF, PF, AF, ZF, SF, OF).
5. **Report** every mismatch with a readable disassembly of the code that caused it.

## Components

| Component | Role |
|---|---|
| Generator | Builds random instruction sequences (arithmetic, logic, shifts, rotates) and initial register values |
| [Keystone](https://www.keystone-engine.org/) | Assembles instruction text into machine code |
| [Unicorn](https://www.unicorn-engine.org/) | Software CPU emulator, used as the reference model |
| Native runner | Runs the same machine code on the host CPU using `mmap` and `ctypes` |
| [Capstone](https://www.capstone-engine.org/) | Disassembles machine code for readable reports |
| Comparator | Compares registers and flags, masking flags that the Intel SDM defines as *undefined* for a given instruction |

## Roadmap

- [ ] Run a single instruction in Unicorn and read back registers and flags
- [ ] Random instruction generator
- [ ] Native execution harness on the host CPU
- [ ] Register and flag comparison with per-instruction undefined-flag masks
- [ ] Mismatch reports with disassembly
- [ ] Command-line options (number of tests, random seed, instruction classes) and unit tests

## Requirements

- Linux or WSL on an x86-64 machine (native execution needs an x86-64 host CPU)
- Python 3.10+

```bash
pip install unicorn capstone keystone-engine
```

## Background

CPU validation teams check new processors by running very large numbers of tests and comparing the
hardware's behavior against a reference model; random tests catch corner cases nobody thought to write by hand.
This project is a small-scale version of that workflow, focused on general-purpose x86-64 instructions.
