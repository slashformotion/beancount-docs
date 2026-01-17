# Why Beancount 3?

Beancount 3 represents a significant evolution of the Beancount project. While the syntax remains largely backward-compatible with v2, the underlying engine and ecosystem have been completely overhauled.

This guide outlines the key improvements and differences for users migrating from v2 or those curious about the architecture.

## 1. Core Architecture & Performance (The C++ Rewrite)

The most significant change in v3 is the move from a pure Python implementation to a C++ core.

*   **v2:** Was primarily Python, with some C extensions for speed. As ledgers grew (with 10+ years of history), loading times could become noticeable (taking seconds to minutes).
*   **v3:** The core logic (parsing, booking, interpolation) is written in C++.

This results in:
*   **Drastic Performance Gains:** Loading and checking large files is orders of magnitude faster.
*   **Scalability:** Beancount 3 can handle massive transaction volumes (decades of data) without UI lag or slow reporting.

## 2. Strictness, Correctness, and Type Safety

Beancount 3 is stricter about accounting invariants. The rewrite allowed the authors to tighten the rules and ensure calculations are even more precise—critical for financial software.

*   **Lot Matching:** The logic for matching lots (e.g., selling stocks) is more robust.
*   **Sign Handling:** Ambiguities in sign handling (mixed positive/negative numbers in confusing contexts) are reduced or disallowed.
*   **Precision:** Floating-point issues are further minimized by the C++ decimal implementation.

## 3. Focus on the Core & Modular Ecosystem

Beancount 3 focuses on doing the core accounting tasks perfectly. It adopts a more modular philosophy, separating the core engine from auxiliary tools.

### Importing Data (Beangulp)
In Beancount v2, the importing framework (`beancount.ingest`) was bundled directly into the main package.
In v3, ingestion is split into a separate project called **Beangulp**.
*   This separates the concerns of *ledger logic* from *data extraction logic*.
*   Beangulp provides a cleaner API and better tools for deduplicating transactions.
*   Existing importers will likely need minor updates to work with Beangulp.

### Modern Tooling
Beancount 3 is designed to work better with modern Python environments.
*   **Fava:** The popular web UI continues to support Beancount 3.
*   **Bean-query:** The SQL engine has been refined, though the syntax (BQL) remains mostly the same.
*   **API Stability:** For developers building tools on top of Beancount, v3 provides a more stable, typed API (via Python bindings to the C++ core).

## 4. Installation and Distribution

Because of the C++ core, installation is slightly different than the pure Python days of v2.

*   **v2:** Simple `pip install beancount`.
*   **v3:** Requires compiling the C++ core or using pre-built binary wheels.
    *   The build system uses **Bazel**, ensuring reproducible builds.
    *   Most users should stick to official binary releases to avoid complex compilation steps.

## Summary for Migrating Users

1.  **Your `.beancount` files likely don't need changes.** The syntax is stable.
2.  **Your importers will need attention.** You will likely need to migrate to Beangulp.
3.  **Your scripts might break.** Internal Python API changes will affect custom tools.
4.  **It will be much faster.**
