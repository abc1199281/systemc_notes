# SystemC Learning Notes

[English] | [繁體中文](README_zh-TW.md)

> **Structured learning notes for the SystemC library** — from top-down conceptual architecture to bottom-up source code analysis.

## Target Audience

Software engineers without hardware RTL background who want to deeply understand SystemC. All explanations aim to be simple enough for a high school student to follow.

## Documentation

| Directory | Language | Description |
|-----------|----------|-------------|
| [en/](en/) | English | Top-down concepts, examples, and diagrams |
| [zh-TW/](zh-TW/) | 繁體中文 | Complete notes: top-down concepts, bottom-up code analysis, examples, and diagrams |

### Content Structure

```
topdown/     — Conceptual architecture docs (simulation engine, events, scheduling, etc.)
code/        — Per-file source code analysis (zh-TW only)
examples/    — SystemC & TLM example walkthroughs
diagrams/    — Architecture overview diagrams
```

## Suggested Reading Order

1. **Start with** [learning-path](en/topdown/learning-path.md) for the recommended reading sequence
2. Each document begins with a **real-life analogy** to build intuition
3. After understanding concepts, dive into `zh-TW/code/` for detailed source code analysis
4. Mermaid diagrams can be viewed on GitHub, VSCode, or any Mermaid-compatible tool

## About

These notes were created to systematically study the [Accellera SystemC](https://systemc.org/) open-source library, covering both the core SystemC kernel and TLM (Transaction-Level Modeling).
