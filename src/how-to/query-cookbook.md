# Query Cookbook

`bean-query` is a powerful command-line tool that lets you extract data from your Beancount file using a SQL-like language called **BQL** (Beancount Query Language).

Unlike standard SQL which queries a database on disk, `bean-query` loads your text file into memory and runs queries against the internal data structures.

> **Note:** For simple text reports (like balances or income statements), see [Running Reports](running-reports.md) instead.

## How to use `bean-query`

### 1. Interactive Mode (Shell)
If you run the command without a query string, you enter an interactive shell. This is best for exploring data.

```bash
$ bean-query main.beancount
Input file: "main.beancount"
Ready with 1000 directives (1500 postings).
beancount> SELECT sum(position) WHERE account ~ "Assets"
...
```

Type `help` inside the shell to see available commands.

### 2. Command Line Mode (Scripting)
You can pass the query directly as a string. This is useful for scripts or quick checks.

```bash
bean-query main.beancount "SELECT sum(position) WHERE account ~ 'Assets'"
```

## Core Concepts

### Available Columns
Every transaction posting has specific columns you can query:
*   `date`: The date of the transaction.
*   `account`: The account name (e.g., `Assets:Bank`).
*   `narration`: The description text.
*   `payee`: The payee text.
*   `position`: The amount and currency (e.g., `10.00 USD`).
*   `balance`: The cumulative balance after this posting.
*   `tags`: A set of tags attached to the transaction.
*   `links`: A set of links attached to the transaction.

### Functions
BQL supports many functions to manipulate data:
*   `year(date)`, `month(date)`: Extract date parts.
*   `root(account, 2)`: Get the parent account (e.g., `Assets:Bank` from `Assets:Bank:Chase`).
*   `sum(position)`: Add up amounts (handles multiple currencies automatically).
*   `abs(amount)`: Absolute value.

---

## Spending Analysis

**How much did I spend on Coffee this year?**
```sql
SELECT sum(position)
WHERE account ~ "Expenses:Food:Coffee"
  AND year(date) = 2024
```

**What are my top 10 expense categories?**
```sql
SELECT account, sum(position) as total
WHERE account ~ "Expenses"
  AND year(date) = 2024
GROUP BY account
ORDER BY total DESC
LIMIT 10
```

## Income Analysis

**Monthly Income by Source**
```sql
SELECT year(date), month(date), root(account, 3), sum(position)
WHERE account ~ "Income"
GROUP BY year(date), month(date), root(account, 3)
ORDER BY year(date), month(date)
```

## Net Worth

**Current Net Worth (Sum of Assets + Liabilities)**
Note: Since Liabilities are negative, summing them gives the net value.
```sql
SELECT sum(position)
WHERE account ~ "Assets|Liabilities"
```

## Auditing

**Find huge transactions (over $1000)**
```sql
SELECT date, narration, account, position
WHERE abs(number(position)) > 1000
ORDER BY date DESC
```

**Find transactions missing a tag**
```sql
SELECT *
WHERE tag IS NULL
```
