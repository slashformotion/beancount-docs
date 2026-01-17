# How to Track Stock Vesting (RSUs)

Restricted Stock Units (RSUs) are a common form of compensation. Tracking them requires handling the income event (vesting) and the subsequent capital gains (selling).

## 1. The Grant
When you are granted RSUs, you don't actually own anything yet. You technically don't need to record this in your main ledger, but you can track it in a separate "Shadow" account if you wish.

*Most users skip this step and start at Vesting.*

## 2. Vesting (The Taxable Event)
When shares vest, two things happen:
1.  You receive shares (Asset).
2.  You pay taxes (Expense), usually by selling some of those shares immediately ("Sell to Cover").
3.  You recognize the total value as Income.

**Example:**
*   You vest 10 shares of `GOOG`.
*   Market price is $100/share. Total Value: $1000.
*   Tax rate is 40%. You "sell" 4 shares to pay $400 in taxes.
*   You keep 6 shares.

```beancount
2024-05-15 * "Employer" "RSU Vest - 10 shares"
  Income:Employer:RSU          -1000.00 USD  ; Total Grant Value
  Expenses:Taxes:Federal         400.00 USD  ; Taxes paid
  Assets:Brokerage:GOOG            6 GOOG {100.00 USD}
```

*   **Income**: We record the full gross amount ($1000).
*   **Asset**: We record the 6 shares we actually kept, with a **cost basis** of the market price at that moment (`{100.00 USD}`).

## 3. Selling
Later, when you sell the shares, you calculate Capital Gains based on that cost basis.

**Scenario:** Price rises to $120. You sell your 6 shares.

```beancount
2024-08-01 * "Broker" "Sell RSUs"
  Assets:Brokerage:GOOG           -6 GOOG {100.00 USD} @ 120.00 USD
  Assets:Brokerage:Cash          720.00 USD
  Income:CapitalGains           -120.00 USD
```

*   **Cost**: $600 (6 * 100).
*   **Proceeds**: $720 (6 * 120).
*   **Gain**: $120.

## Employee Stock Purchase Plan (ESPP)
ESPP is different. You buy shares at a discount.

**Example:**
*   Market Price: $100.
*   Discount: 15% (You pay $85).
*   You buy 10 shares.

```beancount
2024-06-30 * "Employer" "ESPP Purchase"
  Assets:Brokerage:Cash         -850.00 USD ; You paid $850
  Income:Employer:ESPP-Discount -150.00 USD ; The discount is essentially income
  Assets:Brokerage:GOOG           10 GOOG {100.00 USD} ; Cost basis is Fair Market Value
```

*Note: ESPP tax rules are complex (Qualifying vs Disqualifying dispositions). This method sets the cost basis to the FMV ($100), treating the discount as immediate income. Consult a tax professional for your specific reporting needs.*
