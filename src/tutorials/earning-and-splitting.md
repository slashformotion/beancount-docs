# Recording Income and Split Transactions

In the [Getting Started](getting-started.md) tutorial, we recorded a simple expense. Now, let's tackle the other side of the equation: earning money.

We will also learn how to handle "Split Transactions"—transactions that touch more than two accounts. This is essential for recording things like your paycheck, which often has tax deductions, or a supermarket receipt that includes both food and household supplies.

## Step 1: Defining New Accounts

Before we can record income, we need a place to put it. We also need accounts for the deductions we expect (like taxes).

Open your `main.beancount` file and add these open directives:

```beancount
; Income accounts
2000-01-01 open Income:Salary

; Tax expenses
2000-01-01 open Expenses:Taxes:Federal
2000-01-01 open Expenses:Taxes:State
2000-01-01 open Expenses:Taxes:Medicare
```

## Step 2: Understanding Income

In Beancount (and double-entry accounting in general), **Income is recorded as a negative number.**

This is often the most confusing part for beginners. Think of it this way:
*   **Assets** are positive (Money you have).
*   **Income** is negative (The source of that money).

When you get paid, money moves **FROM** your job (Income) **TO** your bank account (Asset).
Since the sum of a transaction must be zero:
`(+Asset) + (-Income) = 0`

## Step 3: Recording a Simple Paycheck

Let's start with a simplified example. You get paid ,000, deposited directly into your checking account.

```beancount
2024-01-15 * "Employer Inc" "Salary"
  Assets:Checking:Chase       2000.00 USD
  Income:Salary              -2000.00 USD
```

Run `bean-check main.beancount`. It should pass.
If you run `bean-report main.beancount balances`, you will see:
*   Assets:Checking:Chase increased by ,000.
*   Income:Salary is -2,000.

## Step 4: The Real World (Split Transactions)

Real paychecks aren't that simple. Usually, your "Gross Pay" is higher, but taxes and insurance are taken out before the money hits your bank.

Let's say your **Gross Pay** is ,000.
*   **Federal Tax** takes 00.
*   **State Tax** takes 00.
*   The remainder (,500) is deposited into your bank.

This is a **Split Transaction**. It involves one source (Income) and multiple destinations (Bank, Tax Expenses).

Add this transaction to your file:

```beancount
2024-01-31 * "Employer Inc" "Salary with deductions"
  Assets:Checking:Chase       2500.00 USD
  Expenses:Taxes:Federal       400.00 USD
  Expenses:Taxes:State         100.00 USD
  Income:Salary              -3000.00 USD
```

### Why does this work?
Sum up the numbers:
`+2500 (Bank) + 400 (Fed) + 100 (State) - 3000 (Income) = 0`

The transaction balances. This accurately reflects reality: you earned ,000, but you only received ,500 in cash because you "spent" 00 on taxes immediately.

## Step 5: Splitting Expenses

The same logic applies to spending money. Suppose you go to "SuperMart" and buy:
*   Groceries (0)
*   A new toaster (Household supplies, 0)

First, ensure you have the accounts:
```beancount
2000-01-01 open Expenses:Household:Supplies
```

Now record the split expense:

```beancount
2024-02-02 * "SuperMart" "Groceries and Toaster"
  Expenses:Food:Groceries       60.00 USD
  Expenses:Household:Supplies   30.00 USD
  Assets:Checking:Chase        -90.00 USD
```

Here, money leaves your Checking account (-90) and splits into two different expense buckets (+60 and +30).

## Summary

*   **Income is negative.** It balances out the positive increase in your Assets.
*   **Transactions can have many lines.** As long as they sum to zero, you can split money across as many accounts as you need.

Next, we will look at how to handle debt and credit cards.
