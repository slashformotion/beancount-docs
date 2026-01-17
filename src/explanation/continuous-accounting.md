# Continuous Accounting

Traditional accounting software (and corporate accounting) relies on **Fiscal Years**. At the end of the year, accountants "close the books."

This involves:
1.  Summing up all Income and Expenses.
2.  Moving that net profit/loss to `Equity:Retained-Earnings`.
3.  Resetting Income and Expenses to zero for Jan 1st.

## Why Beancount is Different

Beancount uses **Continuous Accounting**. It never resets accounts to zero.

### Advantages
1.  **Multi-Year Reporting**: You can run a report for "The last 18 months" or "2010 to 2020" instantly. In a closed system, this requires stitching together multiple closed files.
2.  **Simplicity**: You don't have to create complex "closing entries" every year.
3.  **Data Integrity**: Your file is a single, unbroken stream of history.

### How it works
When you want to see your income for just 2024, you don't look at the raw balance (which is "All Income since the beginning of time"). Instead, you ask Beancount to **calculate the change** in balance between Jan 1, 2024, and Dec 31, 2024.

CLI tools do this automatically when you pass `--year=2024`.
