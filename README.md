# Chrono

Chrono is a **from-scratch implementation of a Git-like version control system**, written in Python, with a focus on **understanding and rebuilding Git internals** rather than wrapping existing Git tooling.

The project reimplements Git’s core data model — repositories, objects, commits, and history traversal — by closely following Git’s on-disk formats and invariants. Chrono is designed as a **learning-oriented but technically faithful** system that demonstrates how Git actually works under the hood.

---

## Motivation

Git is one of the most widely used tools in software engineering, yet its internal design is often treated as a black box. Chrono was built to:

* Understand Git as a **content-addressed object store**, not a diff-based system
* Learn low-level filesystem and storage design concepts
* Practice building a non-trivial CLI system with strict correctness requirements
* Gain hands-on experience with immutable data structures, hashing, and history graphs

Chrono is **not** intended to replace Git. It is an educational reimplementation that prioritizes clarity, correctness, and internal consistency.


## Features

### Repository Management

* Initialize repositories with a Git-compatible directory structure
* Repository discovery from any subdirectory (Git-style behavior)
* Strict validation of repository invariants

### Object Storage (Core Git Model)

* Content-addressed storage using **SHA-1 hashing**
* Immutable object database with automatic deduplication
* Zlib-compressed loose objects stored under `.git/objects/`
* Exact Git object header format: `<type> <size>\0<content>`

### Supported Object Types

* **Blob** — raw file contents
* **Tree** — directory structure snapshots
* **Commit** — snapshots with metadata and parent references
* **Tag** — annotated object references

### Plumbing Commands

* `chrono init` — initialize a new repository
* `chrono hash-object` — hash files into Git objects (optionally write to DB)
* `chrono cat-file` — inspect raw object contents
* `chrono log` — traverse and display commit history

### Commit & History Handling

* RFC-2822–style commit parsing (KVLM format)
* Support for multi-parent commits (merge commits)
* Deterministic serialization to preserve object identity
* History traversal via parent links (DAG traversal)


## What Chrono Intentionally Does NOT Implement

To keep the project focused on core concepts, Chrono does **not** implement:

* Packfiles (`.pack` / `.idx`)
* Network protocols (fetch, push, clone)
* Performance optimizations used in production Git

Loose objects are used exclusively to preserve conceptual clarity.


## Project Structure

```
chrono/
├── chrono.py          # CLI entry point
├── libchrono.py       # Core implementation
├── .git/              # Repository metadata (created at runtime)
└── README.md
```

## Example Usage

Initialize a repository:

```bash
python chrono.py init myrepo
```

Hash a file into a blob object:

```bash
python chrono.py hash-object file.txt
```

Write the object into the repository:

```bash
python chrono.py hash-object -w file.txt
```

Inspect an object’s contents:

```bash
python chrono.py cat-file blob <object-sha>
```

View commit history:

```bash
python chrono.py log
```


## Design Principles

* **Correctness over convenience** — fail fast on invalid states
* **Immutability by design** — objects are never modified after creation
* **Deterministic serialization** — identical data always produces identical hashes
* **Separation of concerns** — CLI, storage, and parsing logic are cleanly separated


## Technical Highlights

* Custom implementation of Git’s content-addressed storage model
* Recursive parsers for commit metadata and history traversal
* Binary-safe I/O for object storage and inspection
* Careful handling of object identity and ordering guarantees


## Learning Outcomes

Building Chrono involved working deeply with:

* Filesystem-based data stores
* Cryptographic hashing and immutability
* Binary data parsing and serialization
* Directed acyclic graphs (commit history)
* CLI tooling and argument parsing

---

## Disclaimer

Chrono is an educational project. While it closely follows Git’s internal data model, it is **not a drop-in replacement for Git** and should not be used for production version control.
