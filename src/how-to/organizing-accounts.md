# How to Organize Your Accounts

One of the first questions new users ask is "What accounts should I have?"

Beancount uses a hierarchical structure (separated by colons `:`) that allows you to be as general or specific as you want.

## The Five Root Types (Strict Rule)
You cannot rename these. Every account **must** start with one of these five roots:

1.  **Assets**: Things you own that have value.
    *   *Examples: Bank accounts, Cash, House, Car, Stocks, Receivables (money owed to you).*
2.  **Liabilities**: Money you owe to others.
    *   *Examples: Credit Cards, Mortgage, Student Loans.*
3.  **Income**: Money flowing *in* to your life.
    *   *Examples: Salary, Interest, Dividends, Gifts Received.*
4.  **Expenses**: Money flowing *out* of your life (consumption).
    *   *Examples: Rent, Food, Taxes, Utilities, Gifts Given.*
5.  **Equity**: The net worth of the ledger.
    *   *Examples: Opening balances, Conversions, Retained Earnings.*

---

## Example 1: Simple Personal Finance
For most people starting out, keep it simple. Don't over-engineer.

```text
Assets:US:BofA:Checking
Assets:US:BofA:Savings
Assets:Vanguard:401k

Liabilities:US:Chase:SapphireCard

Income:US:Employer:Salary
Income:US:Bank:Interest

Expenses:Home:Rent
Expenses:Home:Utilities
Expenses:Food:Groceries
Expenses:Food:Restaurant
Expenses:Transport:Metro
Expenses:Transport:Uber
Expenses:Shopping:Clothing
Expenses:Health:Medical
```

## Example 2: The Freelancer
Freelancers need to separate business and personal expenses.

```text
; Business Assets
Assets:Biz:PayPal
Assets:Biz:Checking

; Personal Assets
Assets:Pers:Checking

; Business Income
Income:Biz:ClientA
Income:Biz:ClientB

; Expenses
Expenses:Biz:Hosting
Expenses:Biz:Software
Expenses:Pers:Food
```

## Tips for Success

### 1. Geography is useful
If you have accounts in multiple countries, put the country code early in the hierarchy:
*   `Assets:US:Chase`
*   `Assets:UK:Barclays`
This groups all your US assets together in reports.

### 2. Institution Names
Using the bank name helps you match physical statements to Beancount accounts:
*   `Liabilities:US:Amex:Gold`
*   `Liabilities:US:Amex:Platinum`

### 3. Don't go too deep
Avoid: `Expenses:Food:Groceries:Fruits:Bananas`.
Better: `Expenses:Food:Groceries`.
*Why?* If you are too specific, classifying transactions becomes a chore. You can always use `#tags` for temporary specificity (like `#wedding` or `#renovation`).

### 4. Group by Fixed vs Variable
Some users like to separate fixed bills from discretionary spending:
*   `Expenses:Fixed:Rent`
*   `Expenses:Fixed:Internet`
*   `Expenses:Variable:Dining`
*   `Expenses:Variable:Hobbies`
