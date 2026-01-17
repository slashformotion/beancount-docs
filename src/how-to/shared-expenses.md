# Managing Shared Expenses

When you share finances with a partner, roommate, or group, you often pay for things on their behalf. Beancount handles this by tracking **Receivables** (money owed to you) and **Payables** (money you owe).

## Scenario 1: You pay, they owe you
You pay $100 for dinner. $50 is for you, $50 is for your friend "Alice".

```beancount
2024-06-01 * "Restaurant" "Dinner with Alice"
  Assets:CreditCard:Visa        -100.00 USD
  Expenses:Food:Dining            50.00 USD
  Assets:Receivable:Alice         50.00 USD
```

*   **Your Expense**: Only $50.
*   **Asset**: You now hold a $50 claim against Alice.

When Alice pays you back:

```beancount
2024-06-05 * "Alice" "Venmo repayment"
  Assets:Bank:Checking            50.00 USD
  Assets:Receivable:Alice        -50.00 USD
```
*The Receivable account is now zero.*

## Scenario 2: They pay, you owe them
Alice pays $100. Your share is $50.

```beancount
2024-06-01 * "Alice" "Paid for dinner"
  Expenses:Food:Dining            50.00 USD
  Liabilities:Payable:Alice      -50.00 USD
```

When you pay her back:

```beancount
2024-06-05 * "Venmo" "Paying Alice back"
  Liabilities:Payable:Alice       50.00 USD
  Assets:Bank:Checking           -50.00 USD
```

## Scenario 3: Netting Out (The "Tabs" approach)
If you live with someone, you might not settle every transaction. You just want to track the running balance.

Use a single account: `Assets:Partner`.
- Positive balance: They owe you.
- Negative balance: You owe them.

**You pay electric bill ($100, split 50/50):**
```beancount
2024-06-01 * "Electric Co"
  Assets:Bank                     -100.00 USD
  Expenses:Utilities                50.00 USD
  Assets:Partner                    50.00 USD
```

**They pay rent ($2000, split 1000/1000):**
```beancount
2024-06-01 * "Partner paid rent"
  Expenses:Housing:Rent           1000.00 USD
  Assets:Partner                 -1000.00 USD
```

**Current Balance of `Assets:Partner`**: `-950.00 USD`. You owe them $950.
You write them a check for $950 to settle up.
