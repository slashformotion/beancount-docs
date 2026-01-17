# Options Reference

Global options configure how Beancount parses and validates your file.

Usage:
```beancount
option "key" "value"
```

## Essential Options

### `title`
The title of your ledger, used in reports.
```beancount
option "title" "My Personal Finances"
```

### `operating_currency`
Defines the currencies that are "yours". This affects how reports aggregate numbers. You can specify this multiple times.
```beancount
option "operating_currency" "USD"
option "operating_currency" "EUR"
```

## Parsing & Accuracy

### `tolerance`
Sets the inferred tolerance for balancing transactions. Defaults to 0.005 for most currencies.
```beancount
option "tolerance" "0.01"
```

### `account_rounding`
Automatically inserts a rounding posting to an Equity account if a transaction is off by a tiny amount (due to currency conversion).
```beancount
option "account_rounding" "Equity:Rounding"
```

## Classification

### `documents`
A list of directories where Beancount should look for external documents (PDFs) to link to transactions.
```beancount
option "documents" "/home/user/finances/docs"
```

### `account_previous_balances`
Beancount can calculate the balance of an account before the first transaction based on `open` date.
```beancount
option "account_previous_balances" "Opening-Balances"
```

## Formatting

### `render_commas`
Whether to render numbers with thousands separators in reports (TRUE/FALSE).
```beancount
option "render_commas" "TRUE"
```

### `plugin_processing_mode`
"default" or "raw". Determines when plugins run. usually "default".
```beancount
option "plugin_processing_mode" "default"
```

## Root Account Naming (Localization)
You can rename the five standard root accounts to suit your language or preference.

### `name_assets`, `name_liabilities`, `name_equity`, `name_income`, `name_expenses`
Override the default names.

```beancount
option "name_assets"      "Actifs"
option "name_liabilities" "Passifs"
option "name_equity"      "Capitaux"
option "name_income"      "Revenus"
option "name_expenses"    "Depenses"
```
