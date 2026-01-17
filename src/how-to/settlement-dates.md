# How to Track Settlement Dates

In financial trading, there is often a delay between the **Trade Date** (when you agreed to the deal) and the **Settlement Date** (when the cash actually moves).

*   **Trade Date:** You bought the stock today. You own it now.
*   **Settlement Date:** The cash leaves your account in 2 days (T+2).

Beancount generally uses a single date per transaction. This guide shows you how to track both using an intermediate "Pending" account.

## The Strategy

Instead of moving cash directly from `Assets:Checking` to `Assets:Stock`, we move it through `Assets:Pending:Transfers`.

1.  **On Trade Date:** Record the stock purchase and a debt to the pending account.
2.  **On Settlement Date:** Record the cash movement clearing the debt.

## Example: Buying Stock

You buy 10 shares of AAPL for $150 on **June 1st**. The cash will settle on **June 3rd**.

### Step 1: The Trade (June 1)

You legally own the stock now. You "owe" the cash to the settlement clearing house.

```beancount
2024-06-01 * "Buy AAPL"
  Assets:Brokerage:AAPL         10 AAPL {150.00 USD}
  Assets:Pending:Transfers    -1500.00 USD
```

*   **Stock:** Account increases (You own it).
*   **Pending:** Account decreases (You owe money).

### Step 2: The Settlement (June 3)

The bank actually transfers the money.

```beancount
2024-06-03 * "Settlement for AAPL"
  Assets:Pending:Transfers     1500.00 USD
  Assets:Checking             -1500.00 USD
```

*   **Pending:** Account returns to zero.
*   **Checking:** Cash leaves your bank.

## Using the `pad` Directive

If you import your bank data automatically, your bank will only show the transaction on June 3rd. Your brokerage might show the trade on June 1st.

You can use `pad` to fill the gap automatically without manually writing the settlement transaction every time.

1.  **Trade (From Brokerage Importer):**
    ```beancount
    2024-06-01 * "Buy AAPL"
      Assets:Brokerage:AAPL         10 AAPL {150.00 USD}
      Assets:Pending:Transfers
    ```
    *(Note: We leave the second leg empty or let the importer calculate it)*

2.  **Cash (From Bank Importer):**
    ```beancount
    2024-06-03 * "ACH Transfer"
      Assets:Checking             -1500.00 USD
      Assets:Pending:Transfers
    ```

3.  **Pad Directive:**
    If you don't have the exact matching transaction, you can tell Beancount to "fill the gap" in the Pending account.

    ```beancount
    2024-06-02 pad Assets:Pending:Transfers Equity:Timing-Differences
    ```

    *Use `pad` with caution! It effectively "invents" a transaction to make balances match.*

## Benefits

*   **Accuracy:** Your stock holdings are correct on the trade date (important for dividends and voting rights).
*   **Reconciliation:** Your bank balance matches the bank statement dates (settlement dates).
