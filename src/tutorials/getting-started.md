# Getting Started with Beancount 3

This tutorial guides you through creating your first Beancount ledger. You will learn how to set up the file structure, define your accounts, and record your first transactions.

By the end of this tutorial, you will have a working accounting system that can produce a balance sheet.

## Step 1: Installation Check

Before we begin, ensure you have Beancount 3 installed in your environment.

```bash
python3 -m beancount --version
```
*If this command fails, please refer to the installation instructions for your operating system.*

## Step 2: The Options

Create a new empty text file named `main.beancount`. This will be your master ledger.

The first thing every Beancount file needs is some basic configuration. Add these lines to the top of your file:

```beancount
option "title" "My Personal Finances"
option "operating_currency" "USD"
```

*   `title`: Gives your ledger a name, which appears in reports.
*   `operating_currency`: Tells Beancount which currency is "yours." You can have multiple operating currencies (e.g., `"USD", "EUR"`), but you must have at least one.

## Step 3: Defining Accounts

Unlike some other plain text accounting tools, Beancount requires you to **declare** an account before you use it. This strictness helps catch typos (like typing `Assets:Chekcing` by mistake).

We use the `open` directive to create accounts. We usually date these back to a time before we started tracking (like 1970 or 2000).
> **Tip:** Not sure how to name your accounts? Check out the [Organizing Accounts](../how-to/organizing-accounts.md) guide.

Add these lines to your file:

```beancount
; Define the equity account for initial balances
2000-01-01 open Equity:Opening-Balances

; Define our main bank account
2000-01-01 open Assets:Checking:Chase

; Define a category for food expenses
2000-01-01 open Expenses:Food:Groceries
```

## Step 4: Setting the Opening Balance

When you start tracking your finances, you likely already have money in your bank account. We need to record this.

In double-entry accounting, money cannot appear out of thin air. It must come *from* somewhere. For opening balances, it comes from `Equity`.

Let's say you have $1,000 in your account on Jan 1st, 2024. Add this transaction:

```beancount
2024-01-01 * "Opening Balance for Checking"
  Assets:Checking:Chase       1000.00 USD
  Equity:Opening-Balances    -1000.00 USD
```

Notice that the two numbers sum to zero (+1000 and -1000). This is the core rule: **Every transaction must balance to zero.**

## Step 5: Recording a Purchase

Now, let's spend some money. On Jan 2nd, you bought groceries for $50.

```beancount
2024-01-02 * "Whole Foods" "Weekly groceries"
  Expenses:Food:Groceries       50.00 USD
  Assets:Checking:Chase        -50.00 USD
```
> **Learn More:** See [Recording Transactions](../how-to/recording-transactions.md) for more complex examples like salary and split expenses.

*   We **increased** our Expenses (Expenses are usually positive).
*   We **decreased** our Assets (money left the checking account).

## Step 6: Verification

Beancount comes with a powerful tool called `bean-check`. It reads your file and ensures:
1.  All syntax is correct.
2.  All transactions balance to zero.
3.  You haven't used any undeclared accounts.
4.  Your balance assertions (if any) are correct.

Run this in your terminal:

```bash
bean-check main.beancount
```

> **Reference:** For more details on command line tools, see the [CLI Reference](../reference/cli.md).

If the command runs silently (no output), your file is perfect! If there are errors, it will tell you the line number and the problem.

## Step 7: Generating a Report

Now that we have data, let's see a report. The `bean-report` tool can generate many views. Let's look at the **balances**:

```bash
bean-report main.beancount balances
```

You should see output similar to:
```text
Assets:Checking:Chase      950.00 USD
Equity:Opening-Balances  -1000.00 USD
Expenses:Food:Groceries     50.00 USD
```

You now have a fully functional double-entry accounting system!
