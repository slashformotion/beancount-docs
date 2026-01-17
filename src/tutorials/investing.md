# Advanced Investing: Stocks and Capital Gains

This tutorial moves beyond simple income and expenses to handle **Assets held at Cost**. This is the foundation of tracking investments, stocks, and currency trading in Beancount.

## Step 1: Defining Accounts

Investment accounts look like regular asset accounts, but they hold **Commodities** (like "AAPL" or "GOOG") instead of just Currency (USD).

Add these to your `main.beancount`:

```beancount
2000-01-01 open Assets:Brokerage:Cash        USD
2000-01-01 open Assets:Brokerage:Stocks      AAPL, GOOG
2000-01-01 open Income:Dividends
2000-01-01 open Income:Capital-Gains
```

## Step 2: Buying Stock (Cost Basis)

When you buy stock, you aren't just transferring value; you are acquiring specific "lots" of an asset at a specific price. Beancount tracks this **Cost Basis**.

On Jan 1st, you buy 10 shares of AAPL at $150 each.

```beancount
2024-01-01 * "Buy AAPL"
  Assets:Brokerage:Stocks      10 AAPL {150.00 USD}
  Assets:Brokerage:Cash     -1500.00 USD
```

*   **`10 AAPL`**: The quantity and commodity.
*   **`{150.00 USD}`**: The **Cost** per share. This attaches a "price tag" to these specific 10 shares.

## Step 3: Buying More (Multiple Lots)

On Feb 1st, the price drops. You buy 5 more shares at $140.

```beancount
2024-02-01 * "Buy more AAPL"
  Assets:Brokerage:Stocks       5 AAPL {140.00 USD}
  Assets:Brokerage:Cash      -700.00 USD
```

You now own 15 shares of AAPL, but Beancount sees them as two distinct **Lots**:
1.  10 shares @ $150
2.  5 shares @ $140

## Step 4: Selling and Realizing Gains

On March 1st, the price hits $160. You decide to sell 5 shares.
But *which* 5 shares? The expensive ones ($150) or the cheap ones ($140)?

This is the **Booking Method**. By default, Beancount lets you choose specific lots (Specific Identification).

Let's sell 5 shares from the first batch ($150).

```beancount
2024-03-01 * "Sell AAPL"
  Assets:Brokerage:Stocks      -5 AAPL {150.00 USD} @ 160.00 USD
  Assets:Brokerage:Cash       800.00 USD
  Income:Capital-Gains        -50.00 USD
```

### Breaking it down:
1.  **`-5 AAPL {150.00 USD}`**: We are removing 5 shares that had a *cost* of $150.
2.  **`@ 160.00 USD`**: We sold them at a *market price* of $160.
3.  **`Assets:Brokerage:Cash 800.00 USD`**: We received $800 cash (5 * $160).
4.  **`Income:Capital-Gains -50.00 USD`**: The profit.

**The Math:**
*   Cost of sold shares: 5 * $150 = $750.
*   Cash received: 5 * $160 = $800.
*   Profit: $800 - $750 = $50.

Since Income is negative, we record `-50.00 USD`.
The transaction balances: `-750 (Stock Cost) + 800 (Cash) - 50 (Profit) = 0`.

## Step 5: Dividends

Dividends are simply income.

```beancount
2024-03-15 * "Apple Dividend"
  Assets:Brokerage:Cash        15.00 USD
  Income:Dividends            -15.00 USD
```

## Summary

1.  Use `{price}` to attach cost basis when buying.
2.  Use `{cost} @ price` when selling to calculate P/L.
3.  Profit is the difference between the Cost Basis and the Sell Price.
