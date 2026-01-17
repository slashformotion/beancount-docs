# Managing Credit Cards and Loans

In this tutorial, we introduce a new type of account: **Liabilities**.

A Liability represents money you owe to someone else. The most common example is a Credit Card. Unlike a Debit card (which pulls money *directly* from your Assets), a Credit Card increases your debt, which you pay off later.

## Step 1: Defining a Credit Card Account

Credit cards live under the `Liabilities` hierarchy. Let's open an account for a Visa card.

Add this to your `main.beancount`:

```beancount
2000-01-01 open Liabilities:CreditCard:Visa
```

## Step 2: Buying with Credit

When you swipe your credit card, two things happen:
1.  You incur an **Expense** (e.g., you bought dinner).
2.  You incur a **Liability** (you now owe the bank money).

In Beancount, Liabilities are generally recorded as **negative numbers**, just like Income.
*   **Assets** are positive (What you own).
*   **Liabilities** are negative (What you owe).

However, unlike Income, we don't usually talk about "increasing negative debt." We just say the balance gets "more negative."

Let's record a 0 dinner on the Visa card:

```beancount
2024-02-14 * "Romantic Bistro" "Valentine's Dinner"
  Expenses:Food:Dining           50.00 USD
  Liabilities:CreditCard:Visa   -50.00 USD
```

Notice:
*   Expense is Positive (+50).
*   Liability is Negative (-50).
*   Sum is Zero.

If you run `bean-report main.beancount balances`, your Visa account will show `-50.00 USD`. This means you owe 0.

## Step 3: Buying More Stuff

Let's buy something else. A 0 book.

```beancount
2024-02-15 * "Bookstore" "New Novel"
  Expenses:Entertainment:Books   20.00 USD
  Liabilities:CreditCard:Visa   -20.00 USD
```

(Make sure you `open Expenses:Entertainment:Books` if you haven't already!)

Your Visa balance is now `-70.00 USD`.

## Step 4: Paying the Bill

A month later, you receive your credit card statement. It says you owe 0. You pay it from your Checking account.

This transaction is a **Transfer**. You are moving money from an Asset to a Liability.
*   Checking Account decreases (-70).
*   Credit Card debt decreases (moves from -70 back towards 0, so +70).

```beancount
2024-03-15 * "Chase Bank" "Paying off Visa bill"
  Liabilities:CreditCard:Visa    70.00 USD
  Assets:Checking:Chase         -70.00 USD
```

After this transaction:
*   Your Checking account has 0 less.
*   Your Visa account balance is `0.00 USD`. You are debt-free!

## Common Confusion

New users often ask: *"Why do I record the expense when I buy the item? Why not when I pay the bill?"*

In double-entry accounting (and accrual accounting), you record the expense **when it happens**.
1.  **Feb 14:** You ate the dinner. That is when you "spent" the value.
2.  **Mar 15:** You moved cash to satisfy the debt. That is just a transfer of funds.

This gives you a more accurate picture of your spending. If you only recorded expenses when you paid the bill, it would look like you spent /run/current-system/sw/bin/bash in February and 0 on "Credit Card Bill" in March. That doesn't tell you *what* you bought!

## Summary

1.  **Spending:** Increase Expense (+), Increase Debt (-).
2.  **Paying Bill:** Decrease Debt (+), Decrease Asset (-).
3.  Liabilities usually have negative balances.

Next, we will learn how to make sure these numbers match your actual bank statements using **Balance Assertions**.
