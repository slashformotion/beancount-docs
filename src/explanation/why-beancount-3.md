# Why Beancount 3?

Beancount 3 represents a significant evolution of the Beancount project.

## Performance
By moving the core logic to C++, Beancount 3 can process large ledger files (with decades of data) much faster than Beancount 2.

## Type Safety and Correctness
The rewrite allowed the authors to tighten the rules and ensure that calculations are even more precise, which is critical for financial software.

## Modern Tooling
Beancount 3 is designed to work better with modern Python environments and provides a more stable API for developers building tools on top of it.

## Focus on the Core
Beancount 3 focuses on doing the core accounting tasks perfectly. It leaves the visualization and importing to companion projects (like [Fava](../how-to/using-fava.md)), ensuring the engine remains lean and reliable.
