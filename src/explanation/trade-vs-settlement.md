# Trade vs. Settlement Dates

In financial markets, a transaction happens in two distinct stages:
1.  **Trade Date (T)**: The legal agreement to buy/sell is executed. The price is fixed.
2.  **Settlement Date (T+2)**: The legal ownership is transferred and cash is exchanged.

## The Accounting Dilemma
This delay creates a gap in your records.
*   **On Date T**: You "own" the stock risk (if price moves, you gain/lose), but you still have the cash in your account.
*   **On Date T+2**: The cash actually leaves.

Which date should you use for your books?

## Model 1: Trade Date Accounting (Standard)
Beancount (and the IRS) generally favors **Trade Date Accounting**.
*   **Concept**: You record the asset change immediately on Day T.
*   **Reality**: Your Cash account in Beancount will show a lower balance than your actual bank balance for 2 days.
*   **Why**: Tax liability usually attaches on the Trade Date. The holding period (Long vs Short term capital gains) starts counting from the Trade Date.

## Model 2: Settlement Date Accounting (Cash Basis)
Some users prefer to wait until the cash moves.
*   **Concept**: You ignore the trade until T+2.
*   **Problem**: Your Portfolio Value is wrong for 2 days. You might miss tax deadlines if the trade happens on Dec 31st but settles Jan 2nd.

## Model 3: Full Accrual (The "Pending" Asset)
To be perfectly accurate during the gap, you must recognize that you owe money (a Payable) or are owed money (a Receivable).

**On Trade Date:**
You don't lose Cash yet; you gain a specific liability ("Settlement Payable").
```beancount
2024-06-01 * "Buy Stock"
  Assets:Brokerage:Stock       10 AAPL {100.00 USD}
  Liabilities:Brokerage:Settlement    -1000.00 USD
```

**On Settlement Date:**
The liability is extinguished by Cash.
```beancount
2024-06-03 * "Settlement"
  Liabilities:Brokerage:Settlement     1000.00 USD
  Assets:Brokerage:Cash               -1000.00 USD
```

While Model 3 is the most "correct" for an auditor, Model 1 (Trade Date) is the standard recommendation for personal finance in Beancount because it simplifies data entry significantly.
