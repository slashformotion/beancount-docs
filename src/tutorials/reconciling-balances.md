# Reconciling with Balance Assertions

You have been entering transactions manually. But humans make mistakes. Maybe you typed 0.00 instead of .00, or you forgot to record that coffee you bought last Tuesday.

How do you trust your numbers?

In Beancount, we use **Balance Assertions**. This is the "killer feature" that makes plain-text accounting robust.

## Step 1: What is a Balance Assertion?

A balance assertion is a statement of fact. You are telling Beancount:
*"I checked my bank website, and on this specific date, the balance was exactly X."*

Beancount will then calculate the balance of all your transactions up to that date.
*   If they match: Great! Nothing happens.
*   If they differ: Beancount stops with an error, telling you exactly how much you are off by.

## Step 2: Adding an Assertion

Let's say you log into your bank website on **2024-02-01**. The website says your Checking account has **,450.00**.

In your file, you might have entered transactions that leave you with ,450.00, or maybe ,445.00 if you missed a coffee.

Add this line to your file:

```beancount
2024-02-01 balance Assets:Checking:Chase  1450.00 USD
```

Note the syntax:
1.  **Date:** The morning of this date (before any transactions *on* this date happen).
2.  **Directive:** `balance`
3.  **Account:** The account to check.
4.  **Amount:** The expected amount.

## Step 3: Running the Check

Run the checker:
```bash
bean-check main.beancount
```

**Scenario A: Success**
If the command outputs nothing (or just "no errors"), your history matches the bank's reality perfectly.

**Scenario B: Failure**
If you forgot a transaction, you might see an error like this:

```text
main.beancount:45: Balance failed for Assets:Checking:Chase: expected 1450.00 USD but accumulated 1455.00 USD
```

This tells you:
1.  You expected ,450.
2.  Beancount calculated ,455 based on your transactions.
3.  You have  too much in your Beancount file. This means you likely forgot to record a  expense (or recorded a  income twice).

## Step 4: Finding the Error

When an assertion fails, you know the error happened **between the last successful assertion and this one**.

This is why frequent assertions are good.
*   If you assert once a year, you have to search 365 days of transactions for the error.
*   If you assert once a month (when your statement arrives), you only have to search one month.

To fix the error above ( difference), you would look through your bank statement, find the missing  coffee, and add the transaction:

```beancount
2024-01-28 * "Starbucks" "Forgot this one"
  Expenses:Food:Coffee          5.00 USD
  Assets:Checking:Chase        -5.00 USD
```

Run `bean-check` again. The calculated balance will drop by , matching the assertion (450), and the error will disappear.

## Best Practices

1.  **Assert regularly:** Every time you receive a monthly PDF statement from your bank, add a `balance` directive for the date of the statement.
2.  **Pad directives:** Sometimes (especially when starting), you can't find the error. Beancount has a `pad` directive that auto-inserts a transaction to make the balance match. Use this sparingly! (See the Reference docs for `pad`).
3.  **Order matters:** Assertions check the balance at the **beginning** of the day. If you have transactions on `2024-02-01`, the assertion `2024-02-01 balance ...` checks the state *before* those transactions.

## Summary

You have now completed the core tutorials!
1.  **Basics:** Created accounts and simple transactions.
2.  **Income:** Handled salary and split transactions.
3.  **Liabilities:** Managed credit card debt.
4.  **Reconciliation:** Used assertions to ensure accuracy.

You are ready to manage your own finances. Check the **How-to Guides** for specific scenarios like tracking investments, shared expenses, or multi-currency trips.
