# Short Sales

A **Short Sale** is when you sell an asset you don't own (by borrowing it), hoping to buy it back later at a lower price.

In Beancount terms, this means holding a **negative number of units held at cost**.

## The Constraint
By default, Beancount enforces a constraint: you cannot have a negative inventory of lots.
*   If you hold `10 GOOG {100 USD}`, you can sell at most 10.
*   If you try to sell 15, you get an error.

## Enabling Short Sales
To allow short selling, you generally need to relax this constraint or use a dedicated account where the negative balance is expected.

### Method 1: Dedicated Short Account
Separate your "Long" and "Short" positions.

```beancount
2024-06-01 * "Short Sell"
  Assets:Brokerage:Cash          1000.00 USD
  Liabilities:Brokerage:Shorts    -10 GOOG {100.00 USD}
```
Here, we use a **Liability** account to track the short position. A negative liability is... well, usually a liability is already negative (credit). Wait.

In Beancount:
*   Assets are Positive.
*   Liabilities are Negative.

If you are Short, you **owe** the stock. It is a Liability.
So `Liabilities:Brokerage:Shorts` should have a **negative** number of units.

**Closing the Short (Covering):**
You buy the stock back.

```beancount
2024-07-01 * "Cover Short"
  Liabilities:Brokerage:Shorts     10 GOOG {100.00 USD} @ 90.00 USD
  Assets:Brokerage:Cash          -900.00 USD
  Income:CapitalGains            -100.00 USD
```
*   You "buy" 10 units to neutralize the -10 units.
*   Cost Basis match: `{100.00 USD}`.
*   Price paid: `$900`.
*   Profit: `$100`.

## Caveats
*   **Dividends**: If you are short, you *pay* dividends instead of receiving them. This is an Expense.
*   **Interest**: You usually pay margin interest on the borrowed value.
