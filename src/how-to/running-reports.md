# How to Run Reports

Beancount is a command-line tool. While you can use GUIs like [Fava](https://beancount.github.io/fava/) (a separate web interface for Beancount), understanding the CLI tools gives you the most power.

## The Main Command: `bean-report`

The basic syntax is:
`bean-report [filename] [report-name]`

### 1. `balances` (The Snapshot)
Shows the final balance of every account at the end of history.
```bash
bean-report main.beancount balances
```

**Common Flags:**
*   `--at=2023-01-01`: See what the balances were on a specific date.

### 2. `income` (The P&L)
Shows your profit and loss (Income and Expenses) for the period. By default, it calculates the total for the entire history of the file.
```bash
bean-report main.beancount income
```

**Common Flags:**
*   `--begin=2023-01-01`: Start calculating from this date.
*   `--end=2024-01-01`: Stop calculating at this date.

### 3. `check`
Validates the file (same as `bean-check`).
```bash
bean-report main.beancount check
```

---

## The SQL Interface: `bean-query`

For custom reports, Beancount provides a SQL-like query language. This is incredibly powerful.

Start the interactive shell:
```bash
bean-query main.beancount
```

Then type your query:

**Example 1: Find all coffee purchases**
```sql
SELECT date, narration, amount
WHERE account ~ "Expenses:Food" AND narration ~ "Coffee"
```

**Example 2: Monthly spending on Food**
```sql
SELECT year(date), month(date), sum(position)
WHERE account ~ "Expenses:Food"
GROUP BY year(date), month(date)
```

**Example 3: Export to CSV**
You can run a query directly from the command line and save it to a CSV file for Excel/Google Sheets.
```bash
bean-query main.beancount 'HBOX "SELECT * WHERE account ~ 'Assets' "' -f csv > assets.csv
```

---

## Visualizing with Fava

While not part of the core Beancount tool, almost every user installs **Fava**. It is a web interface that runs locally.

For a detailed guide on using its charts and filters, see [Using Fava (Web UI)](using-fava.md).

