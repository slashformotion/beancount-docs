# Portfolio Returns

A common question is: "What is my investment return?" (e.g., "I made 8% this year").

Beancount **does not** calculate percentage returns (IRR or TWR) natively in the core engine. It focuses on accounting correctness (Cost Basis and Capital Gains). However, you can extract the data needed to calculate this.

## The Challenge
To calculate returns, you need to know:
1.  The value of the portfolio at the start.
2.  The value at the end.
3.  The exact date and amount of every **cash flow** in and out.

Since Beancount tracks all of this, the data is there.

## Solution 1: Fava
Fava has a built-in "Holdings" view that attempts to calculate returns for your assets. This is the easiest way for most users.

## Solution 2: Scripts
The standard way to calculate returns in the Beancount ecosystem is to use a script to extract the cash flows and compute the **Internal Rate of Return (IRR)**.

Beancount provides a helper library `beancount.plugins.ira_contribs` which can help track contributions, but for returns, community scripts are often used.

## Calculating Simple Gain (Profit)
You can easily see your total profit (Market Value - Cost Basis) in `bean-query`.

```sql
SELECT
  account,
  sum(convert(position, 'USD')) as market_value,
  sum(cost(position)) as cost_basis,
  (sum(convert(position, 'USD')) - sum(cost(position))) as unrealized_gain
WHERE account ~ "Assets:Investments"
GROUP BY account
```

*Note: This query requires that you have updated prices for your commodities.*
