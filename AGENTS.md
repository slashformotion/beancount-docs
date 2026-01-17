# Agent Guidelines for Beancount-Docs

This repository contains the source for the Beancount 3 documentation website, built using `mdbook`.

## Build and Development

### Environment Setup
This project uses Nix for environment management.
- **Enter shell:** `nix develop` or `nix shell nixpkgs#mdbook nixpkgs#beancount`
- **Dependencies:** `mdbook`, `beancount` (v3).

### Core Commands
- **Build the book:** `mdbook build`
- **Serve locally:** `mdbook serve` (starts a web server with live reloading)
- **Watch for changes:** `mdbook watch`
- **Check syntax:** `bean-check src/path/to/ledger.beancount` (used to verify examples)

### Testing & Validation
Since this is a documentation project, "testing" primarily involves:
1. **Link Checking:** `mdbook build` (watch for warnings/errors).
2. **Beancount Syntax:** All code blocks marked as `beancount` should be valid Beancount 3 syntax.
   - Run a single check: `bean-check <file_path>`
3. **Balance Assertions:** Use the `balance` directive to verify account states in examples.
   - Syntax: `YYYY-MM-DD balance Account:Name  123.45 USD`
   - These are automatically verified when running `bean-check` on the file.
4. **Internal Consistency:** Ensure all chapters are listed in `src/SUMMARY.md`.

## Code Style & Conventions

### Knowledge Base
Refer to `AGENTS_DATA.md` for a comprehensive list of Beancount invariants, syntax rules, and known missing documentation topics. Use this file to ensure technical accuracy.

### Documentation Framework: Diátaxis
Always categorize new content into one of the four Diátaxis quadrants:
- **Tutorials:** Learning-oriented. Step-by-step guides for beginners.
- **How-to Guides:** Task-oriented. Solutions to specific problems.
    - *Cookbooks:* Collections of recipes (e.g., "Common Queries") belong here.
- **Reference:** Information-oriented. Technical descriptions (syntax, CLI, Plugins).
- **Explanation:** Understanding-oriented. Context and deep dives.
    - *Separation of Concerns:* Do not mix "How to do X" with "The history/theory of X". If a topic needs both, create two linked pages (e.g., "How to Close Books" and "Explanation of Continuous Accounting").

### Markdown Style
- **Headers:** Use ATX-style headers (e.g., `# Header`).
- **Formatting:** Use standard Markdown. Avoid HTML tags unless absolutely necessary.
- **Line Length:** No strict limit, but keep paragraphs readable.
- **Lists:** Use `-` for unordered lists.

### Beancount 3 Specifics
- **Explicit Versioning:** Always mention Beancount 3. Avoid features specific to Beancount 2 (like `plugin` directives unless explicitly requested).
- **Account Naming:** Use standard root accounts: `Assets`, `Liabilities`, `Income`, `Expenses`, `Equity`.
- **Date Format:** Always `YYYY-MM-DD`.
- **Currency:** Use `USD` in examples unless another currency is more appropriate for context.

### File Structure
- `src/SUMMARY.md`: The table of contents. Every `.md` file in `src/` must be linked here.
- **Sidebar Titles:** The link text in `SUMMARY.md` should match the H1 header of the referenced file exactly.
- `src/README.md`: The landing page/introduction.
- Use descriptive subdirectories (e.g., `src/how-to/`, `src/reference/`).

### Language & Tone
- **Language:** English (US).
- **Tone:** Professional, direct, and helpful. Use "Simple English" – avoid jargon where a simpler word suffices.
- **Explanations:** Focus on *why* something is done in the "Explanation" section and *how* in the "How-to" section.
- **Interlinking:** When appropriate, add relevant links to the docs for the user to go see what is being referenced.

### Error Handling
- If `mdbook build` fails, check `src/SUMMARY.md` for broken paths first.
- If a `bean-check` fails on an example, ensure the `open` directives exist for all accounts used.
