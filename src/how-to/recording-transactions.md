# How to Record Transactions

Transactions describe the movement of commodities (usually money) between accounts. While simple income and expense transactions are common, real life is often more complex.

This guide covers the standard patterns you will encounter.

## The Anatomy of a Transaction

A transaction is a single unit that ensures money is neither created nor destroyed (except through Income/Equity).

```beancount
2024-01-10 * "Payee" "Narration / Description"
  Account1     -10.00 USD
  Account2      10.00 USD
```

*   **Date**: `YYYY-MM-DD`. Strict requirement.
*   **Flag**: `*` (cleared/confirmed) or `!` (pending).
*   **Payee**: (Optional) Who did you trade with?
*   **Narration**: (Optional) What was this for?
*   **Postings**: The lines indented below. You can have as many as you want, as long as they sum to zero.

---

## 1. Complex Income (Salary with Deductions)
Real paychecks are rarely just "Income -> Bank". They include taxes, 401k contributions, and insurance. You record all of these in a single transaction.

> **Why is Income negative?**
> In Beancount, Income is money flowing *from* the outside world *into* your accounts. To make the math balance to zero, Income is recorded as a negative number.
> [Read the full explanation here](../explanation/double-entry.md#why-is-income-negative).

```beancount
2024-01-15 * "Tech Corp" "Payroll #42"
  Assets:Checking:Chase        2500.00 USD  ; Net pay deposited
  Expenses:Taxes:Federal        500.00 USD
  Expenses:Taxes:State          200.00 USD
  Assets:401k:PreTax            300.00 USD  ; Contribution to retirement
  Expenses:Insurance:Health     100.00 USD
  Income:Salary               -3600.00 USD  ; Gross Pay (negative)
```
*Note that the Gross Pay (-3600) balances out all the positive numbers (2500+500+200+300+100).*

## 2. Shared Expenses (Reimbursements)
If you pay for lunch for a friend and they pay you back later, you shouldn't record the full amount as *your* expense.

**Scenario:** You pay $40. $20 is your food, $20 is for Alice.

```beancount
2024-02-01 * "Restaurant" "Lunch with Alice"
  Assets:Checking:Chase        -40.00 USD
  Expenses:Food:Dining          20.00 USD
  Assets:Receivable:Alice       20.00 USD
```

Later, when Alice pays you back:

```beancount
2024-02-05 * "Alice" "Venmo repayment"
  Assets:Checking:Chase         20.00 USD
  Assets:Receivable:Alice      -20.00 USD
```
*Your `Assets:Receivable:Alice` account is now zero.*

## 3. Foreign Currency (Vacation)
When you use a card abroad, the bank converts the currency. You should record the actual cost to you (in your home currency) or the exact conversion if you want to track the foreign currency.

**Simple approach (Convert at cost):**
You bought a €10 coffee, and your bank charged you $11.20.
```beancount
2024-03-10 * "Cafe Paris" "Coffee"
  Expenses:Travel:Food          11.20 USD
  Assets:Checking:Chase        -11.20 USD
```

**Advanced approach (Tracking rates):**
Explicitly stating the exchange rate using `@`.
```beancount
2024-03-10 * "Cafe Paris" "Coffee"
  Expenses:Travel:Food          10.00 EUR @ 1.12 USD
  Assets:Checking:Chase        -11.20 USD
```

## 4. Metadata, Tags, and Links
You can attach extra data to transactions to help with filtering later.

> **Deep Dive:** For full syntax details, see the [Syntax Reference](../reference/syntax.md#the-transaction-block).

*   **Tags**: Use `#tagname` for categories that cross-cut accounts (like `#vacation2024` or `#tax-deductible`).
*   **Links**: Use `^linkname` to group related transactions (like an invoice and its payment).
*   **Metadata**: Key-value pairs for specific details.

```beancount
2024-04-01 * "Hotel" "Stay in Tokyo" #vacation2024 ^invoice-1234
  booking_id: "XYZ-999"
  Liabilities:CreditCard      -200.00 USD
  Expenses:Travel:Lodging      200.00 USD
```
