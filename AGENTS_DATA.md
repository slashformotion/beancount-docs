# Beancount Knowledge Base & Missing Data

This file contains extracted invariants, reference data, and identified gaps from the official Beancount documentation. Use this as a source of truth for generating new content.

## 1. Syntax Invariants

### General Rules
*   **Dates**: `YYYY-MM-DD`. ISO 8601.
*   **Strings**: Double quotes `"`. Multiline allowed.
*   **Comments**: `;` starts a comment. Ignored by parser.
*   **Ordering**: Directives are sorted by date.
    *   Within a date: `open`, `close`, `balance`, `pad`, `note`, `document` are processed *before* transactions.
*   **Account Names**: Must start with one of 5 roots: `Assets`, `Liabilities`, `Equity`, `Income`, `Expenses`.
    *   Components: `[A-Z][A-Za-z0-9\-]*`. Separated by `:`.
*   **Commodities**: `[A-Z][A-Z0-9\-\_\.\']{0,22}[A-Z0-9]`. Max 24 chars. No spaces.

### Directives Reference

#### `open`
*   **Syntax**: `YYYY-MM-DD open Account [ConstraintCurrency,...] ["BookingMethod"]`
*   **Constraints**: Optional comma-separated list of allowed currencies.
*   **Booking Methods**: `STRICT` (default), `FIFO`, `LIFO`, `NONE` (no matching/average).
*   **Behavior**: Implicitly asserts balance is zero at open.

#### `close`
*   **Syntax**: `YYYY-MM-DD close Account`
*   **Behavior**: Error if posting occurs after this date. Does *not* implicitly assert zero balance (you should add `balance` before `close`).

#### `commodity`
*   **Syntax**: `YYYY-MM-DD commodity Currency`
*   **Purpose**: Attach metadata (e.g., `name`, `export`, `precision`). Optional.

#### `txn` (Transaction)
*   **Syntax**:
    ```beancount
    YYYY-MM-DD [flag] ["Payee"] "Narration" [#tags] [^links]
      key: value
      Account  Amount [{Cost}] [@ Price]
      ...
    ```
*   **Flags**: `*` (Cleared/Verified), `!` (Pending/Incomplete).
*   **Payee vs Narration**:
    *   `"Narr"` -> Narration only.
    *   `"Payee" "Narr"` -> Payee and Narration.
    *   `"Payee" ""` -> Payee only.
*   **Postings**:
    *   **Amount**: `10 USD`.
    *   **Cost**: `{10.00 USD}` (Per unit) or `{{100.00 USD}}` (Total).
        *   Used for Assets/Liabilities to track lots.
        *   Mandatory for reduction if booking method is STRICT.
    *   **Price**: `@ 1.20 EUR` (Per unit) or `@@ 12.00 EUR` (Total).
        *   Used for currency conversion or recording market value.
        *   *Ignored* for balancing if Cost is also present.
*   **Balancing Rules**:
    1.  Amount only -> Weight = Amount.
    2.  Price only -> Weight = Amount * Price.
    3.  Cost only -> Weight = Amount * Cost.
    4.  Cost & Price -> Weight = Amount * Cost (Price is ignored for math, used for history).
*   **Interpolation**: One posting can have a missing amount. Beancount calculates it automatically.

#### `balance`
*   **Syntax**: `YYYY-MM-DD balance Account Amount`
*   **Behavior**: Asserts the balance of *that specific commodity* at the **start** of the day (before transactions on that date).
*   **Scope**: Only checks the specified currency. Other commodities in the account are ignored.

#### `pad`
*   **Syntax**: `YYYY-MM-DD pad Account SourceAccount`
*   **Behavior**: Inserts a transaction on this date to bring the balance of `Account` to match the *next* `balance` assertion.
*   **Restriction**: Generally only works for currencies, not commodities held at cost (lots).

#### `note`
*   **Syntax**: `YYYY-MM-DD note Account "Text"`
*   **Behavior**: Attaches a comment to an account's journal.

#### `document`
*   **Syntax**: `YYYY-MM-DD document Account "/path/to/file"`
*   **Behavior**: Links a file to the account context.

#### `price`
*   **Syntax**: `YYYY-MM-DD price Commodity Amount`
*   **Behavior**: Adds an entry to the historical price database. Does not affect account balances.

#### `event`
*   **Syntax**: `YYYY-MM-DD event "Type" "Value"`
*   **Usage**: Tracking location, employer, address changes.

#### `query`
*   **Syntax**: `YYYY-MM-DD query "Name" "SQL"`
*   **Behavior**: Experimental. Defines a named BQL query.

#### `custom`
*   **Syntax**: `YYYY-MM-DD custom "Type" args...`
*   **Usage**: Prototyping new features, Fava options, budgets.

#### `plugin`
*   **Syntax**: `plugin "module.name" "config"`
*   **Behavior**: Loads Python module to transform entries.

#### `include`
*   **Syntax**: `include "path/to/file.beancount"`
*   **Behavior**: Parses included file. Relative paths are relative to the including file.

## 2. Options Reference

Global configuration using `option "key" "value"`.

### Display & formatting
*   `title`: Ledger title.
*   `operating_currency`: Main currencies for reports (can appear multiple times).
*   `render_commas`: `TRUE` or `FALSE`.

### Parsing & Precision
*   `inferred_tolerance_default`: Explicit tolerance (e.g. `USD:0.01`).
*   `inferred_tolerance_multiplier`: Multiplier for inferred tolerance (default 0.5).
*   `infer_tolerance_from_cost`: Boolean.
*   `account_rounding`: Account to post rounding errors to.
*   `account_previous_balances`: Account for Opening Balances.
*   `account_previous_earnings`: Account for Retained Earnings (previous years).
*   `account_current_earnings`: Account for Net Income (current year).
*   `conversion_currency`: Currency for zero-rate conversions (default `NOTHING`).

### Accounts & Booking
*   `booking_method`: Default booking method (`STRICT`, `FIFO`, `LIFO`).

### Root Account Renaming
*   `name_assets`
*   `name_liabilities`
*   `name_equity`
*   `name_income`
*   `name_expenses`

### System
*   `documents`: Directory root for document scanning.
*   `plugin_processing_mode`: `default` or `raw`.
*   `long_string_maxlines`: Threshold for warning.

## 3. Inventory & Trading Logic

*   **Inventory**: A bag of lots. `10 GOOG {100 USD}` and `5 GOOG {120 USD}` are distinct.
*   **Reduction**: To reduce (sell), you must match an existing lot parameters.
    *   `Assets:Stock -5 GOOG {100 USD}` matches the first lot.
    *   Ambiguous matches invoke the `booking_method`.
*   **Average Cost**: Not natively supported in v2 strict core (requires workarounds or v3 features/plugins).
*   **Stock Splits**: Handled by selling all old units and buying new units (discontinuity in history) or using plugins.

## 4. Beancount Query Language (BQL)

*   **Structure**: `SELECT <targets> FROM <entry-filter> WHERE <posting-filter>`
*   **Clauses**:
    *   `SELECT`: List of columns/functions.
    *   `FROM`: Filter whole transactions (e.g. `has_tag('trip')`).
    *   `WHERE`: Filter specific postings (e.g. `account ~ 'Food'`).
    *   `GROUP BY`: Aggregation keys.
    *   `ORDER BY`: Sorting.
    *   `PIVOT BY`: 2D table generation.
*   **Functions**:
    *   `COST(position)`, `UNITS(position)`, `DAY(date)`, `YEAR(date)`.
    *   `SUM(position)`, `FIRST(date)`, `LAST(date)`, `COUNT(position)`.
    *   `PARENT(account)`.

## 5. Known Missing Guides (Diátaxis Gaps)

We should eventually create pages for these topics found in the official docs:

*   **How-to**:
    *   **Short Sales**: Handling negative units held at cost (requires care).
    *   **Settlement Dates**: Tracking Trade vs Settlement dates (using `pad` or aux dates).
    *   **Exporting Data**: Generating CSVs from `bean-query`.

*   **Reference**:
    *   **BQL Reference**: A complete syntax guide for `bean-query`.

## 6. Beancount 3 Specifics
*   **Beangulp**: Separate import framework.
*   **C++ Core**: Faster.
*   **Strictness**: More rigid about lots and signs.
