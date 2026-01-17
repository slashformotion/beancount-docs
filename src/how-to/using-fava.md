# Using Fava (Web UI)

**Fava** is the standard web interface for Beancount. It transforms your text file into an interactive financial dashboard.

## Installation & Start

```bash
pip install fava
fava main.beancount
```
Open `http://localhost:5000` in your browser.

> **Tip:** Press `?` anywhere in Fava to see keyboard shortcuts.

---

## The Dashboard (Indicators)
The dashboard shows your Net Worth and upcoming events.

### Up-to-Date Indicators
Fava can show colored dots next to accounts to indicate if they have been reconciled recently.

To enable this, add metadata to your `open` directive:
```beancount
2000-01-01 open Assets:Bank:Chase
  fava-uptodate-indication: TRUE
```

*   🟢 **Green**: The last entry is a passed balance check.
*   🔴 **Red**: The last entry is a failed balance check.
*   🟡 **Yellow**: The last entry is not a balance check.
*   ⚪ **Grey**: No activity for a long time (default 60 days).

---

## Filtering
Fava's filter bar is extremely powerful.

### Time Filter
Supports natural language and intervals:
*   `2024`: The whole year.
*   `2024-Q1`: First quarter.
*   `2024-03`: March.
*   `year - 1`: Last year.
*   `month`: This month.

### Account Filter
Accepts regular expressions to match account names:
*   `Assets:US` matches all US assets.
*   `^Assets` matches only Assets (no Income/Expenses).

### Advanced Filter
Filter by tags, links, metadata, or amounts.
*   `#vacation`: Only show transactions with this tag.
*   `payee:"Starbucks"`: Filter by payee.
*   `units > 100`: Only transactions larger than 100.
*   `any(account:"Expenses:Food")`: Entries where *at least one* posting hits food.
*   `all(-account:"Expenses")`: Exclude all expenses.

---

## The Editor
Fava includes a full Beancount text editor in the browser.
*   **Auto-complete**: Type an account name or payee to see suggestions.
*   **Validation**: Errors are highlighted in red immediately.
*   **Formatting**: Use `Align Amounts` to clean up your file.

---

## Importing Data
Fava provides a GUI for `beangulp` importers.

1.  Configure your importers in python (see [Writing Importers](writing-importers.md)).
2.  Add the configuration to your file:
    ```beancount
    custom "fava-option" "import-config" "my_import_config.py"
    custom "fava-option" "import-dirs" "/path/to/downloads"
    ```
3.  Go to the **Import** tab in Fava.
4.  Drag and drop CSV files onto the importer name.
5.  Fava will extract transactions and let you review/accept them before writing to your file.

---

## Configuration Options
Fava has its own set of options defined using the `custom` directive.

### Fiscal Year
Change the start of your year (e.g., UK tax year starts April 6).
```beancount
custom "fava-option" "fiscal-year-end" "04-05"
```

### Invert Signs
If you prefer to see Income/Liabilities as positive numbers (Net Profit view):
```beancount
custom "fava-option" "invert-income-liabilities-equity" "true"
```

### Language
Force a specific language (default is auto-detected).
```beancount
custom "fava-option" "language" "fr"
```
