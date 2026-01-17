# How to Record Trades

Tracking investments requires recording the **cost basis** (what you paid) so you can calculate capital gains when you sell.

## Buying Assets
When you buy an asset (like a stock), you record the cost per share using curly braces `{}`.

> **Why the curly braces?** Beancount tracks distinct "lots" of inventory. See [How Inventories Work](../explanation/inventories.md) for a deep dive.

```beancount
2024-05-01 * "Buy AAPL"
  Assets:Brokerage:Cash      -1500.00 USD
  Assets:Brokerage:AAPL         10 AAPL {150.00 USD}
```
*Here, we bought 10 shares of AAPL at $150.00 each.*

## Selling Assets
When you sell, you must specify which "lot" you are selling by matching the original cost basis. You also record the selling price using `@`.

The difference between the **cost** `{...}` and the **price** `@` is your Capital Gain (or Loss).

```beancount
2024-06-01 * "Sell AAPL"
  Assets:Brokerage:AAPL         -5 AAPL {150.00 USD} @ 160.00 USD
  Assets:Brokerage:Cash        800.00 USD
  Income:CapitalGains          -50.00 USD
```
*We sold 5 shares. Cost: $750 (5 * 150). Proceeds: $800 (5 * 160). Profit: $50.*

## Taxable vs. Tax-Exempt Accounts

The syntax for the trade is the same, but the **Income** account you use for the gains should differ.

### 1. Taxable Account
In a standard brokerage account, every sale is a taxable event. You should book gains to an Income account that reflects taxable income.

```beancount
2024-06-01 * "Sell Stock in Taxable"
  Assets:Taxable:AAPL           -5 AAPL {150.00 USD} @ 160.00 USD
  Assets:Taxable:Cash          800.00 USD
  Income:Taxable:CapitalGains  -50.00 USD
```

### 2. Tax-Exempt / Tax-Advantaged Account (IRA, 401k)
In accounts like an IRA, you don't pay taxes on individual trades. However, **Beancount still requires the transaction to balance.** You still made a profit, even if the IRS doesn't care yet.

To handle this, map the gains to an Income account specifically for that tax shelter. This keeps it separate from your taxable income reports.

```beancount
2024-06-01 * "Sell Stock in IRA"
  Assets:IRA:AAPL               -5 AAPL {150.00 USD} @ 160.00 USD
  Assets:IRA:Cash              800.00 USD
  Income:IRA:Gains             -50.00 USD
```

By separating `Income:Taxable:CapitalGains` and `Income:IRA:Gains`, you can easily filter your reports to see only your taxable obligations.
