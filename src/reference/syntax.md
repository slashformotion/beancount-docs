# Beancount 3 Syntax Reference

Beancount uses a precise but simple text format.

## General Rules
*   **Indentation**: Postings must be indented (usually 2 spaces). Directives must start at the beginning of the line.
*   **Comments**: Any text following a `;` is ignored.
*   **Strings**: Payees and Narrations must be in double quotes `"`.

## Directives

### 1. `option`
Configures global settings.
```beancount
option "title" "My Finances"
```

### 2. `plugin`
Loads Python extensions.
```beancount
plugin "beancount.plugins.auto_accounts"
```

### 3. `open`
Initializes an account. Usually dated before the first transaction.
```beancount
2000-01-01 open Assets:Bank:Chase
```
You can also restrict the currencies allowed in an account:
```beancount
2000-01-01 open Assets:Bank:Chase USD, EUR
```
**Syntax:** `open Account [Constraint1, Constraint2] ["BookingMethod"]`

### 4. `close`
Marks an account as closed. The balance *must* be zero on this date.
```beancount
2024-12-31 close Assets:Bank:OldAccount
```

### 5. `commodity`
Declares a currency or commodity. Optional, but useful for metadata.
```beancount
1998-01-01 commodity USD
  name: "US Dollar"
```

### 6. `price`
Records the market price of a commodity on a specific date.
```beancount
2024-06-01 price AAPL 155.00 USD
```

### 7. `balance`
Asserts that the balance of an account matches a number *at the end of that day*.
```beancount
2024-01-01 balance Assets:Bank:Chase 1500.00 USD
```

### 8. `pad`
Automatically inserts a transaction to make the balance of an account match a future `balance` directive.
The difference is booked to the source account provided.

```beancount
; Calculate the difference needed to reach the next balance check
; and book it to Equity:Opening-Balances
2024-01-01 pad Assets:Bank:Chase Equity:Opening-Balances

2024-01-02 balance Assets:Bank:Chase 1500.00 USD
```
*Use with caution!* It effectively hides missing transactions.

### 9. `custom`
Used to define generic directives that can be handled by scripts or plugins.
```beancount
2024-01-01 custom "budget" "Expenses:Food" "monthly" 500.00 USD
```

---

## The Transaction Block

The most common block in the file.

> **Examples:** See [Recording Transactions](../how-to/recording-transactions.md) for practical examples.

```beancount
YYYY-MM-DD flag "Payee" "Narration" #tag ^link
  meta-key: meta-value
  Account1    Amount1 Currency
  Account2    Amount2 Currency
```

*   **Flag**: `*` (Completed) or `!` (Incomplete).
*   **Payee/Narration**: At least one string is required. Beancount assumes the first string is the payee if there are two strings, or just the narration if there is only one.
*   **Tags**: `#tagname`. Used for categorization.
*   **Links**: `^linkname`. Used to group transactions.
*   **Metadata**: Key-value pairs. Values can be strings, numbers, dates, or booleans.

## Balancing Rules
Beancount enforces that every transaction sums to zero.
*   **Amount Only**: Weight is simply the amount (e.g., `10 USD`).
*   **Cost**: Weight is `Amount * Cost` (e.g., `10 STOCK {100 USD}` = `1000 USD`).
*   **Price**: Weight is `Amount * Price` (e.g., `10 EUR @ 1.2 USD` = `12 USD`).
*   **Cost & Price**: Price is **ignored** for balancing. Weight is `Amount * Cost`.

### Cost Basis `{}` and Price `@`
Used for trading and conversions.

*   `{10.00 USD}`: **Cost**. I bought this item for 10 USD.
*   `@ 12.00 USD`: **Price**. The market value of this item is 12 USD per unit.
*   `@@ 120.00 USD`: **Total Price**. The total value of the entire lot is 120 USD.

Example: Buying 10 shares at $10 each.
```beancount
2024-01-01 * "Buy Stock"
  Assets:Brokerage      10 STOCK {10.00 USD}
  Assets:Cash         -100.00 USD
```
