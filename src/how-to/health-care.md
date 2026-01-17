# Health Care & Insurance

Medical billing is complex. A single doctor's visit can generate multiple bills, insurance adjustments, and reimbursements over months. Beancount's **Link** feature (`^`) is perfect for this.

## Tracking Claims (The "Incident")
Use a unique link identifier for each medical event.

**Step 1: The Visit**
You go to the doctor. You pay a $30 copay.
```beancount
2024-03-01 * "Dr. Smith" "Checkup" ^claim-2024-03-smith
  Expenses:Medical:Doctor          30.00 USD
  Assets:CreditCard               -30.00 USD
```

**Step 2: The Bill Arrives**
A month later, you get a bill for the remaining $150 that insurance didn't cover.
```beancount
2024-04-01 * "Dr. Smith" "Remaining balance" ^claim-2024-03-smith
  Expenses:Medical:Doctor         150.00 USD
  Assets:CreditCard              -150.00 USD
```

**Step 3: Insurance Reimbursement**
Oops, insurance decided to cover $50 of that after all.
```beancount
2024-04-15 * "Aetna" "Reimbursement" ^claim-2024-03-smith
  Assets:Bank                      50.00 USD
  Expenses:Medical:Doctor         -50.00 USD
```

By clicking the `^claim-2024-03-smith` link in Fava, you can see the entire history of this specific medical event and calculate the true final cost ($130).

## Health Savings Accounts (HSA)
HSAs are triple-tax-advantaged accounts. Treat them like any other investment asset.

**Contribution (Payroll)**
```beancount
2024-01-15 * "Paycheck"
  Assets:Bank                    2000.00 USD
  Assets:HSA:Cash                 100.00 USD
  Income:Salary                 -2100.00 USD
```

**Spending from HSA**
When you use your HSA card, you are spending directly from that asset.
```beancount
2024-03-01 * "Pharmacy" "Prescription"
  Expenses:Medical:Meds            15.00 USD
  Assets:HSA:Cash                 -15.00 USD
```

**Investing HSA**
If you invest your HSA funds, track the commodities just like a brokerage account.
```beancount
2024-06-01 * "Buy Fund"
  Assets:HSA:VTSAX             5 VTSAX {100.00 USD}
  Assets:HSA:Cash             -500.00 USD
```
