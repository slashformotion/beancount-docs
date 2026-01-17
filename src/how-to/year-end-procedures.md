# Year-End Procedures

Beancount uses **Continuous Accounting**, which means you do not *need* to close out your books at the end of the year like a traditional business. Your `Income` and `Expense` accounts just keep accumulating history.

However, many users like to perform a "Year-End Check" to lock in their history.

## 1. Verify Balances
On Dec 31st (or Jan 1st), log into all your banks and check the exact balance. Add a `balance` assertion for **every** asset and liability account.

```beancount
2024-01-01 balance Assets:Bank:Checking   1234.56 USD
2024-01-01 balance Liabilities:Visa      -450.00 USD
```

This ensures that no future edits can accidentally break your history for 2023.

## 2. Generate Reports
Since you don't zero out accounts, you must filter by date to see your annual performance.

```bash
# Income Statement for 2023
bean-report main.beancount income --year=2023
```

## 3. (Optional) "Clear" Equity
If you *really* want to zero out your Income/Expense accounts (simulating a "closing"), you can transfer their balances to `Equity:Retained-Earnings`.

**Note:** This is manual and usually discouraged in Beancount because it destroys the ability to run multi-year reports easily.

## 4. Archive Documents
If you store receipts, now is a good time to zip up the `2023/` folder.
