# Zero

**Zero** is a nano‑class terminal text editor with modern ergonomics that stay dormant until you ask for them.

> **Zero overhead. Real editing.**

Zero is designed for one thing above all else: **instant availability**.
If your editor is not ready the moment you need it, it is already too slow.

---

## Why Zero Exists

Terminal editors today fall into two camps:

* Minimal but archaic (e.g. pico, nano)
* Modern but heavy (e.g. Micro)

Zero occupies the missing middle:

> **The fastest possible terminal editor that still feels modern.**

It matches pico/nano on cold startup and idle memory, while offering a carefully chosen subset of Micro‑like features — all **lazy by default**.

---

## Design Principles (Non‑Negotiable)

* **Nano‑class cold startup** (< 10ms)
* **Nano‑class idle RSS** (~2–4MB)
* **Single static binary**
* **Terminal‑native (PTY only)**
* **Non‑modal editing**
* **No plugins**
* **No scripting runtimes**
* **No always‑on background services**

If a feature violates any of the above, it does not belong in Zero.

---

## What Zero Is

* A **fast, non‑modal terminal editor**
* A **drop‑in replacement for nano**
* A **micro‑like experience without micro‑like cost**
* A **tool, not a platform**

## What Zero Is Not

* An IDE
* A plugin ecosystem
* A programmable editor
* A GUI application
* A Neovim alternative

---

## Feature Overview

### Tier‑0 (Always On — Nano‑Class)

These features are available immediately at startup and define Zero’s baseline behavior.

* Open / save files instantly
* Non‑modal typing
* Arrow‑key navigation
* Gap‑buffer editing
* Linear undo / redo
* Status bar (file, cursor, dirty state)
* Safe exit with unsaved changes prompt
* Efficient screen diff rendering

### Tier‑1 (Lazy — Micro‑Like)

These features exist, but are **never initialized** until explicitly triggered.

* Syntax highlighting (Tree‑sitter, incremental)
* Search and replace
* Mouse support
* Clipboard integration
* Multiple cursors
* Split views (shared buffer)
* Config file loading
* Large‑file handling mode

Idle Zero behaves like nano.
Active Zero behaves like a modern editor.

---

## Performance Targets

| Metric       | Target      |
| ------------ | ----------- |
| Cold startup | ~3–7 ms     |
| Binary size  | ~0.8–1.5 MB |
| Idle RSS     | ~2–4 MB     |
| Dependencies | Minimal     |

Zero prioritizes **perceived speed** and **predictability** over feature breadth.

---

## Technical Architecture (High Level)

```
+-------------------------+
| Terminal (Ghostty, SSH) |
+-----------+-------------+
            |
+-----------v-------------+
| Zero Core (Rust)        |
|  - Gap buffer           |
|  - Undo stack           |
|  - Renderer             |
+-----------+-------------+
            |
+-----------v-------------+
| Minimal Syscall Layer   |
| (Rust / Zig)            |
+-------------------------+
```

### Language Split

* **Rust (~75–85%)** — editor core, safety, maintainability
* **Zig (~15–25%)** — minimal syscall / startup / IO paths

This yields the best balance of **raw performance**, **binary size**, and **long‑term sanity**.

---

## Buffer Model

Zero uses a **gap buffer** as its primary text structure:

* Single contiguous allocation
* Cache‑friendly
* Ideal for config files and small‑to‑medium edits

Large files automatically fall back to a chunked read‑only view, activating full editing only when required.

---

## Rendering Strategy

* Fixed grid (rows × columns)
* Back‑buffer diffing
* ANSI escape output only for changed cells
* Single flush per frame

Zero never redraws the entire screen unless forced.

---

## Undo Model

* Linear undo / redo
* Fixed‑size ring buffer
* Oldest operations dropped deterministically

No branching trees. No snapshots. No memory surprises.

---

## Configuration

Configuration is **optional** and loaded **after the first frame**.

* Location: `~/.config/zero/zero.toml`
* Defaults are compiled in
* Config never blocks startup

---

## Usage

```bash
zero file.txt
```

Set as default editor:

```bash
export EDITOR=zero
export VISUAL=zero
```

---

## Explicit Non‑Goals

Zero will **never** support:

* Plugin systems
* Embedded scripting (Lua, JS, WASM)
* Always‑on LSP
* Inline AI / completions
* GUI rendering
* Theming engines

These are not postponed features — they are **rejected features**.

---

## Philosophy

> Speed is preserved not by writing faster code,
> but by refusing to run code at all.

Zero is intentionally boring, predictable, and strict.
That is its advantage.

---

## Status

🚧 **Pre‑v0.1** — architecture locked, implementation in progress.

Initial milestones focus exclusively on:

* Startup time
* Idle memory
* Core editing correctness
