# AT&T Unix System III — Source Tree

This repository contains the source tree for AT&T's Unix System III. The materials here are provided for educational, historical and research purposes only — to study how early Unix systems were structured and implemented.

## 1 — Introduction

What is SysIII?
- Unix System III (SysIII) is one of the early Unix releases from AT&T (early 1980s). It contains the kernel sources, userland utilities, libraries and build scripts used to produce a working UNIX system of that era.

Why am I releasing this?
- I'm publishing this source primarily for education, preservation and historical study. The goal is to make it easier for students, researchers and enthusiasts to read and learn from the original system structure and code. This repository is not intended as a supported or production operating system.

## 2 — Directory structure (inside src/)

Below is a listing of the items found in src/ of this repository and a short description of what each contains or represents.

| Name | Type | Description |
|------|------|-------------|
| `:mk` | file (build helper) | Build include / make fragment used by the top-level build system. Contains macros and rules used while building components. |
| `:mkcmd` | file (build helper) | Make fragment with rules/variables for building userland commands. |
| `:mkhead` | file (build helper) | Make fragment that helps install or manage header files during the build. |
| `:mklib` | file (build helper) | Make fragment that contains rules for building and installing libraries. |
| `:mkstand` | file (build helper) | Make fragment for building standalone utilities or boot/standalone code. |
| `:mkuts` | file (build helper) | Make fragment for building uts (kernel/system) related components. |
| `Makefile` | file | Main Makefile for the src tree: coordinates builds of the kernel, libraries and userland. |
| `arglist` | file | Small helper file used by build scripts (argument list or config fragment). |
| `graphics.sh` | file (script) | Helper script used for building or installing graphics-related tools or assets. |
| `ldflags` | file | Linker flags used for building binaries in this tree. |
| `whatis.sh` | file (script) | Script used to generate `whatis`/database entries for installed commands and manpages. |
| `cmd/` | directory | Userland commands and utilities (the traditional /bin, /usr/bin style programs). |
| `lib/` | directory | Library sources (C libraries and other shared/static libraries used by userland programs). |
| `head/` | directory | Kernel and system header files (interface definitions, system call numbers, data structures). |
| `uts/` | directory | Kernel/system sources — core kernel code (machine-independent system and kernel components). |
| `stand/` | directory | Standalone programs or bootstrap/bootloader-related code and tools. |
| `games/` | directory | Game programs that were historically distributed with Unix. |
| `dev.d/` | directory | Device description fragments used by the build system to define device drivers or device nodes. |
| `glib.d/` | directory | (Build fragment) grouping for general libraries or library build metadata. |
| `gutil.d/` | directory | (Build fragment) grouping for graphics/utilities-related build metadata. |
| `stat.d/` | directory | (Build fragment) used to generate installation tables or stats during packaging/installation. |
| `toc.d/` | directory | (Build fragment) table-of-contents or install metadata used by packaging/install scripts. |
| `whatis.d/` | directory | (Build fragment) metadata used to generate the `whatis` database or related indexes. |

Notes:
- Items whose names end with `.d` are typically directories used by the original build system to organize metadata or grouped source fragments.
- Files beginning with `:` are (historically) include fragments consumed by the Makefile(s) — they act like modular Makefile snippets or build configuration pieces.

## 3 — Disclaimer

This repository contains historical Unix System III sources that may be proprietary to AT&T or other rights holders. By publishing this repository I do not claim any transfer of ownership or rights, and I do not provide any warranty, guarantee, or legal clearance for using or distributing these sources.

- Use at your own risk.
- I provide no warranty of fitness, suitability, or non-infringement.
- If you believe this repository contains material that you own or have rights to and that it should be removed, please contact me so I can address the concern.

## 4 — How to run it?

See Installing-on-SIMH.md for instructions.

---

Acknowledgements: This tree preserves historic Unix System III structure and artifacts for study and research. If you find issues with the repository contents or want to contribute clarifications or historical notes, please open an issue or submit a PR.
