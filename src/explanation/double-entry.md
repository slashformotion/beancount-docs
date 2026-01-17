# Double-Entry Principles

Beancount enforces **Double-Entry Bookkeeping**. This system, codified by Luca Pacioli in 1494, is the standard for businesses worldwide because it is the only way to ensure mathematical consistency in financial records.

## The fundamental rule: Sum = 0

In single-entry accounting (like a simple checkbook register), you just write down "Spent $50". But where did that $50 come from? Did you pay cash? Credit card? Gift card?

In double-entry, you must describe the **movement** of value.
*   Value came FROM somewhere.
*   Value went TO somewhere.

Beancount models this mathematically: **The sum of all postings in a transaction must be zero.**

If you move $10 from your Left Pocket (`-10`) to your Right Pocket (`+10`), the total change in your wealth is zero (`-10 + 10 = 0`).

## The Five Buckets

Everything in accounting fits into one of five buckets. In Beancount, we use positive and negative numbers to represent these, which simplifies the math.

| Category | Normal Sign | Interpretation |
| :--- | :--- | :--- |
| **Assets** | Positive (+) | Money you **have** (Cash, Bank, House) |
| **Liabilities** | Negative (-) | Money you **owe** (Credit Cards, Loans) |
| **Income** | Negative (-) | Money you **earned** (Salary, Interest) |
| **Expenses** | Positive (+) | Money you **spent** (Food, Rent) |
| **Equity** | Negative (-) | Your **Net Worth** (Assets - Liabilities) |

### Why is Income negative?
This is often the most confusing part for beginners. Think of it this way:

**Income is money leaving the "External World" and entering "Your World".**

When your employer pays you $1000:
1.  They lose $1000. Beancount tracks this as `Income:Salary` with a value of `-1000`.
2.  You gain $1000. Your `Assets:Checking` goes up by `+1000`.

`-1000 (Income) + 1000 (Asset) = 0`. The transaction balances.

### Why are Expenses positive?
When you buy coffee for $5:
1.  You lose $5 cash. `Assets:Cash` is `-5`.
2.  You gain $5 worth of "Coffee Value". `Expenses:Food` is `+5`.

`-5 (Asset) + 5 (Expense) = 0`.

## The Equity Equation

Because all transactions sum to zero, the sum of ALL accounts in your file must also equal zero at all times.

`Assets + Liabilities + Income + Expenses + Equity = 0`

If we rearrange this, we get the definition of Net Worth:

`(Assets + Liabilities) + (Income + Expenses) + Equity = 0`

*   `(Assets + Liabilities)` is your current wealth.
*   `(Income + Expenses)` is your profit for the year.
*   `Equity` is your historical wealth.
