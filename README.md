# xv6 Mini Kernel — OSV-T246

An extended [xv6](https://pdos.csail.mit.edu/6.828/) kernel built as Project-Based Learning (PBL) coursework: an **MLFQ scheduling framework**, new system calls, a deadlock-detection framework, and user-space diagnostics.

I led a 4-member team on this project — scheduler design, diagnostic tooling, and testing were divided across members, and the final architecture and results were presented to faculty evaluators.

## What's inside

### MLFQ scheduling framework
- Multi-level priority queues — `mlfq[NQUEUE]` (3 levels) with `enqueue()` / `dequeue()` operations
- Live queue-state introspection from user space via the `mlfqstatus` command, backed by the `mlfqstatus` system call

### System calls
- **`getsysinfo()`** (#22) — returns the number of running processes and total system uptime via `struct sysinfo`; exercised by the `sysinfotest` user program
- **`mlfqstatus()`** — exposes current MLFQ queue occupancy
- **`detect_deadlock()`** — lock-graph based detection, exercised by `dltest`

### Deadlock-detection framework
- `struct lockgraph` for tracking lock dependencies with `detect_deadlock()` in the kernel

### Diagnostics
- `mlfqstatus` — queue-state viewer
- `sysinfotest` — system information tester
- `dltest` — deadlock-detection tester

## Implementation notes

| File | Change |
|---|---|
| `proc.c` / `proc.h` | MLFQ queue structures (`mlfq[NQUEUE]`), `enqueue`/`dequeue`, queue-state printing |
| `syscall.h` / `syscall.c` | New syscall numbers and table entries |
| `sysproc.c` | `sys_getsysinfo()`, `sys_mlfqstatus()`, `sys_detect_deadlock()` implementations |
| `user.h` / `usys.S` | User-space syscall declarations and stubs |
| `Makefile` | New kernel object and user programs (`sysinfotest`, `mlfqstatus`, `dltest`) |

New user programs: `sysinfotest.c`, `mlfqstatus.c`, `dltest.c`, `deadlock.c`.

> Note: the MLFQ queue framework is in place; the active scheduler path currently uses round-robin dispatch.

## Build & run

### Prerequisites
- Linux environment (WSL recommended on Windows)
- GCC
- QEMU

### Build
```bash
make
```

### Run
```bash
make qemu
```

### Debug
```bash
make qemu-gdb
```

## Test commands

```bash
$ sysinfotest      # Running processes: X, Uptime: Y ticks
$ mlfqstatus       # === MLFQ Status === (queue occupancy per level)
$ dltest           # Runs deadlock detection
```

## Tech stack

C · x86 assembly · QEMU · Make
