# Rounding and Precision

Computers often struggle with decimal numbers (e.g., `0.1 + 0.2` might equal `0.30000000000000004`). Beancount avoids this by using **Decimal** types, not floating-point numbers.

## Precision Inference
Beancount does not have a hardcoded list of currencies. Instead, it infers the precision from your file.

If you write:
```beancount
2024-01-01 * "Test"
  Assets:Cash   10.500 USD
```
Beancount will assume USD has 3 decimal places for the entire file.
**Best Practice:** Always be consistent. Use `10.00 USD`, not `10 USD`.

## The Tolerance Problem
In real life, splitting bills or currency conversion often results in fractions of a cent.

Example: Splitting $10.00 three ways.
*   Person A: 3.33
*   Person B: 3.33
*   Person C: 3.33
*   Total: 9.99

The transaction is off by 0.01.

### Automatic Tolerance
Beancount allows a small tolerance (default 0.005) for transactions to balance. If the difference is within this tolerance, Beancount accepts it.

### Accumulating Rounding Errors
If you want to track these discrepancies explicitly (or if they are larger than the tolerance), use the `account_rounding` option.

```beancount
option "account_rounding" "Equity:Rounding"
```

When this is enabled, Beancount will automatically add a posting to `Equity:Rounding` to make the transaction balance perfectly.

```beancount
2024-01-01 * "Split Bill"
  Expenses:Food       10.00 USD
  Assets:A           -3.33 USD
  Assets:B           -3.33 USD
  Assets:C           -3.33 USD
  ; Implicitly adds:
  ; Equity:Rounding  -0.01 USD
```
