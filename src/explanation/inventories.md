# How Inventories Work

In many accounting systems, currencies are simple numbers. In Beancount, any account can hold "Inventory."

## What is Inventory?
Think of an inventory as a mixed bag of items. A single account doesn't just hold "Dollars." It can hold:
*   100 USD
*   50 EUR
*   10 shares of GOOG (bought at $100)
*   5 shares of GOOG (bought at $120)

Beancount tracks each of these "lots" separately.

## Cost Basis: The Specific Lot
When you acquire a commodity (like stock), Beancount remembers the cost.

```beancount
2024-01-01 * "Buy"
  Assets:Brokerage    10 GOOG {100.00 USD}
```

This creates a specific lot in your inventory: `10 GOOG @ 100 USD`.

If you buy more later:
```beancount
2024-02-01 * "Buy More"
  Assets:Brokerage    5 GOOG {120.00 USD}
```

Your `Assets:Brokerage` account now holds two distinct lots. It does **not** hold "15 GOOG at average cost $106". It holds the specific lots.

## Reduction (Selling)
When you sell, you must specify *which* lot you are reducing. Beancount needs to know this to calculate the Capital Gain correctly.

> **How-to:** For step-by-step instructions, see [Recording Trades](../how-to/recording-trades.md).

```beancount
2024-03-01 * "Sell"
  Assets:Brokerage    -5 GOOG {100.00 USD} @ 130.00 USD
```

Here, we explicitly told Beancount "Sell 5 shares from the lot that cost $100".
*   Cost: 5 * 100 = 500
*   Price: 5 * 130 = 650
*   Profit: 150

If you tried to sell `{-5 GOOG}` without specifying the cost `{...}`, Beancount wouldn't know which lot to pick and would raise an error (unless you use a booking method like FIFO, which Beancount 3 can automate in some contexts).

## Strict vs Average Booking

Beancount uses a **Strict Booking** model by default, often called "Specific Identification" in tax terms.

### Why Strict?
In many jurisdictions (like the US), the tax authority allows you to choose which shares you are selling.
*   **Optimization**: If you sell shares with a *high* cost basis, you realize a smaller capital gain (and pay less tax now).
*   **Control**: If you want to hold onto your oldest shares (for long-term capital gains rates), you need to explicitly sell the newer ones.

If Beancount automatically averaged your costs (e.g., merging the $100 and $120 lots into one $106.66 lot), you would lose this ability to optimize and report correctly.

### What about Average Cost?
Some countries (like Canada or the UK) require you to use the **Average Cost Basis** (ACB) method.
Beancount 3 handles this by allowing you to merge lots.
*   **Manual**: You can use a transaction to "convert" multiple specific lots into a single average lot, though this is tedious.
*   **Automatic**: Beancount 3 introduces flexible booking methods that can simulate average cost behavior during reduction, or you can use plugins to enforce it.

However, even with Average Cost, the underlying database in Beancount prefers strictness to ensure auditability. You can always derive an average from specific lots, but you cannot recover specific lots if you strictly averaged them on input.
