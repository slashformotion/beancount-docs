# Beancount Query Language (BQL)

BQL is a SQL-like query language designed specifically for filtering and aggregating double-entry accounting data. It runs on the in-memory data structures of Beancount.

## Query Structure

The basic syntax mirrors SQL:

```sql
SELECT <target1>, <target2>, ...
FROM <entry-filter>
WHERE <posting-filter>
GROUP BY <grouping1>, <grouping2>, ...
ORDER BY <order1>, <order2>, ...
LIMIT <number>
```

## 1. Data Sources (`FROM`)

The `FROM` clause selects which **Entries** (transactions) are considered. If an entry is excluded here, none of its postings will be visible to the rest of the query.

*   `#tagname`: Select entries with a specific tag.
*   `^linkname`: Select entries with a specific link.
*   `has_tag('tagname')`: Standard function form.
*   `has_link('linkname')`: Standard function form.
*   `RE("pattern")`: Match narration or payee against a regex.

**Example:**
```sql
SELECT * FROM has_tag('vacation2024')
```

## 2. Filtering Postings (`WHERE`)

The `WHERE` clause filters individual **Postings** within the selected entries.

*   `account`: The account name (string).
*   `currency`: The commodity currency (string).
*   `date`: The date of the transaction.
*   `year`, `month`, `day`: Date components.
*   `meta(key)`: Access metadata values.

**Operators:**
*   `=`, `!=`: Equality.
*   `<`, `<=`, `>`, `>=`: Comparison.
*   `~`: Regular expression match (case-insensitive).
*   `AND`, `OR`, `NOT`: Logical operators.

**Example:**
```sql
SELECT * WHERE account ~ "Expenses:Food" AND currency = "USD"
```

## 3. Columns and Functions (`SELECT`)

### Data Columns
These are the raw attributes available for every posting:

| Column | Type | Description |
| :--- | :--- | :--- |
| `date` | Date | Date of the transaction. |
| `flag` | String | Transaction flag (`*` or `!`). |
| `account` | String | The account name. |
| `payee` | String | The payee name. |
| `narration` | String | The transaction description. |
| `position` | Position | The held amount (units + cost). |
| `balance` | Position | Running balance (only in journal mode). |
| `id` | ID | Unique internal ID. |

### Simple Functions
These functions operate on a single row/posting.

| Function | Description | Example |
| :--- | :--- | :--- |
| `COST(pos)` | Get the cost inventory of a position. | `COST(position)` |
| `UNITS(pos)` | Get the number of units. | `UNITS(position)` |
| `CURRENCY(pos)` | Get the currency of the position. | `CURRENCY(position)` |
| `ABS(num)` | Absolute value. | `ABS(number)` |
| `NEG(num)` | Negate value. | `NEG(number)` |
| `PARENT(account)` | Get parent account name. | `PARENT(account)` |
| `ROOT(account, n)` | Get the n-th root component. | `ROOT(account, 1)` |
| `JOIN(str, ...)` | Join strings. | `JOIN(payee, narration)` |
| `META(key)` | Get metadata value. | `META('booking_id')` |
| `TODAY()` | Returns current date. | `date > TODAY()` |

### Date Functions
| Function | Description |
| :--- | :--- |
| `YEAR(date)` | Extract year (integer). |
| `MONTH(date)` | Extract month (integer). |
| `DAY(date)` | Extract day (integer). |
| `QUARTER(date)` | Format as "2024Q1". |

### Aggregate Functions
These functions require a `GROUP BY` clause.

| Function | Description |
| :--- | :--- |
| `SUM(pos)` | Sums positions. Handles mixed currencies automatically. |
| `COUNT(val)` | Counts number of rows. |
| `MIN(val)` | Minimum value. |
| `MAX(val)` | Maximum value. |
| `FIRST(val)` | First value in the group sequence. |
| `LAST(val)` | Last value in the group sequence. |

## 4. Interactive Command Shell

You can run BQL queries interactively using the `bean-query` tool.

```bash
bean-query main.beancount
```

Inside the shell:
*   `help`: List available commands.
*   `schema`: Show available columns.
*   `targets`: Show available functions.

## Common Recipes

**Monthly Expenses by Category:**
```sql
SELECT
  year(date) as year,
  month(date) as month,
  root(account, 2) as category,
  SUM(position)
WHERE
  account ~ "Expenses"
GROUP BY year, month, category
ORDER BY year, month, category
```

**Last Reconciled Date per Account:**
```sql
SELECT
  account,
  MAX(date) as last_reconciled
WHERE
  flag = "*"
GROUP BY account
```
