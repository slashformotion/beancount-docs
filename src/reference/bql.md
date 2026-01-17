# Beancount Query Language (BQL)

BQL is a SQL-like language for querying Beancount data.

## Syntax
```sql
SELECT <targets>
FROM <entry-filter>
WHERE <posting-filter>
GROUP BY <groupings>
ORDER BY <ordering>
LIMIT <number>
```

## Clauses

### `SELECT`
Comma-separated list of columns or functions.
*   `SELECT date, account, position`
*   `SELECT last(date), cost(sum(position))`

### `FROM`
Filters whole transactions (entries). If a transaction matches, **all** its postings are considered for the `WHERE` clause.
*   `FROM has_tag('trip')`
*   `FROM year = 2024`

### `WHERE`
Filters individual postings.
*   `WHERE account ~ "Expenses:Food"`
*   `WHERE currency = "USD"`

### `GROUP BY`
Aggregates results. Required if using aggregate functions like `SUM`.
*   `GROUP BY account`
*   `GROUP BY 1, 2` (Column indices)

## Functions

### Simple Functions
*   `COST(pos)`: Returns the cost amount of a position.
*   `UNITS(pos)`: Returns the units (number) of a position.
*   `DAY(date)`, `MONTH(date)`, `YEAR(date)`: Extract date parts.
*   `PARENT(account)`: Returns the parent account name.
*   `ABS(num)`: Absolute value.
*   `NEG(num)`: Negate.

### Aggregate Functions
*   `SUM(pos)`: Sums positions (handles mixed currencies).
*   `COUNT(pos)`: Counts rows.
*   `MIN(val)`, `MAX(val)`: Minimum/Maximum.
*   `FIRST(val)`, `LAST(val)`: First/Last value in the group.

## Examples

**Holdings (at Cost)**
```sql
SELECT account, sum(cost(position))
WHERE account ~ "Assets"
GROUP BY account
```

**Income Statement (2024)**
```sql
SELECT account, sum(position)
WHERE account ~ "Income|Expenses" AND year = 2024
GROUP BY account
```
