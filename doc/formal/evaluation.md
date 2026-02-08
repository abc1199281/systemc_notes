# Documentation Evaluation Report

## Context
User requires a more granular, file-by-file analysis of `ref/systemc/src` (Bottom-Up) and a comprehensive summary of relationships (Top-Down). The current documents are organized by topic phases and are considered to have "insufficient granularity."

## File-by-File Evaluation

| File | Status | Rationale |
| :--- | :--- | :--- |
| `plan.md` | **Delete** | Will be replaced by `doc/code/plan.md` (Bottom-Up) and `doc/topdown/plan.md` (Top-Down). |
| `00-environment-overview.md` | **Keep** | Background on SystemC 3.0.2 environment is still valid. |
| `01-systemc-architecture.md` | **Modify** | Good high-level view, but should be integrated into the new Top-Down plan. |
| `02-kernel-analysis.md` | **Archive** | Topic-based analysis is too broad. Will be replaced by file-specific analysis in `doc/code/kernel/`. |
| `02-kernel-simcontext.md` | **Archive** | Specialized on `sc_simcontext`, but doesn't cover the full file details. |
| `03-communication-signal.md`| **Archive** | Broad summary. Needs more granular file analysis. |
| `04-datatypes-bit-int.md` | **Archive** | Broad summary. Needs more granular file analysis. |
| `05-tracing-overview.md` | **Archive** | Broad summary. |
| `06-tlm-overview.md` | **Archive** | Broad summary. |
| `07-examples-analysis.md` | **Keep/Link** | Useful for practical patterns, can be linked from the new Top-Down analysis. |

## Recommendations
1. **Archive Old Docs**: Move topic-based `.md` files to `doc/formal/archive/` instead of flat-out deleting them, as they contain some useful "Hardware RTL Mapping" notes that the user likes.
2. **New Directory Structure**:
    - `doc/code/{module}/{filename}.md`: One-to-one mapping for Bottom-Up analysis.
    - `doc/topdown/`: For architecture and dependency analysis.
3. **Execution**: Start with `doc/code/plan.md` to list all files in `ref/systemc/src` and prioritize them.

> [!IMPORTANT]
> Awaiting user confirmation to proceed with archiving/deleting these files.
