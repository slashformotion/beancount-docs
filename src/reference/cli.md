# Beancount 3 Command Line Interface

Beancount 3 provides several tools to interact with your ledger files. 

## bean-check
This is the most important command. It checks your file for syntax errors and balance mistakes.

```bash
bean-check my_ledger.beancount
```
If there are no errors, the command will produce no output.

## bean-report
Generates reports from your data. Common reports include:
- `balances`: Shows the balance of all accounts.
- `income`: Shows your income and expenses.

```bash
bean-report my_ledger.beancount balances
```

## bean-query
Allows you to run SQL-like queries on your transactions. This is very powerful for custom analysis.

```bash
bean-query my_ledger.beancount "SELECT date, narration, amount WHERE account ~ 'Expenses'"
```

## bean-format
Automatically cleans up your ledger file by aligning amounts and sorting directives where possible.

```bash
bean-format my_ledger.beancount
```

> **Note**: Beancount 3 focuses on performance and correctness. Always ensure you are running the version compatible with your installation by checking `beancount --version`.
