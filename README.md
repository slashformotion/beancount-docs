# Beancount 3 Documentation (Experimental)

> **⚠️ WARNING: AI-Generated Content**
> This repository is an **experimental project** to verify if an AI Agent can autonomously generate high-quality technical documentation for a complex software project.
>
> This is **NOT** the official documentation for Beancount.
> 
> *   **Official Docs**: [https://beancount.github.io/docs/](https://beancount.github.io/docs/)
> *   **Official Repo**: [https://github.com/beancount/beancount](https://github.com/beancount/beancount)
>
> While every effort has been made to ensure accuracy, the content here may contain hallucinations, outdated information, or syntax errors. Use with caution.

## Project Overview

This is a static site documentation (built with `mdbook`) covering **Beancount 3**, the double-entry bookkeeping system.

The content follows the **Diátaxis** framework and includes:
*   **Tutorials**: Getting started from scratch.
*   **How-to Guides**: Specific tasks like recording trades, using Fava, and writing importers.
*   **Reference**: Syntax, CLI options, and Plugins.
*   **Explanation**: Core concepts like Double-Entry and Continuous Accounting.

## Building Locally

This project uses **Nix** for reproducible builds.

1.  **Clone the repo:**
    ```bash
    git clone https://github.com/your-username/beancount-docs-experiment.git
    cd beancount-docs-experiment
    ```

2.  **Enter the environment:**
    ```bash
    nix develop
    ```

3.  **Serve the site:**
    ```bash
    mdbook serve
    ```
    Open `http://localhost:3000` in your browser.

## Contributing

Since this is an AI experiment, manual contributions might be overwritten by the agent in future iterations. Please open an issue if you find a major discrepancy.

## License

MIT. See `LICENSE` for details.
