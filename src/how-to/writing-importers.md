# Writing Importers with Beangulp

Beancount 3 uses a separate library called `beangulp` for importing data. Beangulp provides a framework to extract transactions from external files (like CSVs or PDFs) and convert them into Beancount directives.

## Prerequisites

You need to install `beangulp`:

```bash
pip install beangulp
```

## The Importer Protocol

To create a custom importer, you must subclass `beangulp.Importer` and implement these methods:

1.  `identify(self, filepath)`: Returns `True` if this importer can handle the given file.
2.  `extract(self, filepath, existing)`: Parses the file and returns a list of Beancount entries.
3.  `account(self, filepath)`: Returns the account associated with this file (used for filing).
4.  `date(self, filepath)`: Returns the date associated with this file (used for filing).

## Example: A Simple CSV Importer

Let's say you have a bank CSV that looks like this:
```csv
Date,Description,Amount
2024-01-15,Coffee Shop,-5.00
2024-01-16,Salary,3000.00
```

Here is a complete importer for this format.

```python
import csv
from datetime import datetime
from beangulp import Importer
from beancount.core import data, amount
from decimal import Decimal

class MyBankImporter(Importer):
    def __init__(self, account_name):
        self.account_name = account_name

    def identify(self, filepath):
        # Only accept CSV files
        if not filepath.endswith(".csv"):
            return False
            
        # Peek at the header to be sure
        with open(filepath) as f:
            header = f.readline().strip()
        return header == "Date,Description,Amount"

    def account(self, filepath):
        return self.account_name

    def date(self, filepath):
        # Optional: Extract the date from the filename or the last transaction
        # For simplicity, we'll return the date of the last transaction in the file
        last_date = None
        with open(filepath) as f:
            reader = csv.DictReader(f)
            for row in reader:
                last_date = datetime.strptime(row['Date'], '%Y-%m-%d').date()
        return last_date

    def extract(self, filepath, existing=None):
        entries = []
        
        with open(filepath) as f:
            reader = csv.DictReader(f)
            for row in reader:
                # 1. Parse metadata
                date = datetime.strptime(row['Date'], '%Y-%m-%d').date()
                desc = row['Description']
                amt = Decimal(row['Amount'])
                
                # 2. Create the transaction
                meta = data.new_metadata(filepath, reader.line_num)
                txn = data.Transaction(
                    meta, date, "*", None, desc, data.EMPTY_SET, data.EMPTY_SET, []
                )

                # 3. Add postings
                # The main posting (the bank account)
                txn.postings.append(
                    data.Posting(self.account_name, amount.Amount(amt, "USD"), 
                               None, None, None, None)
                )
                
                # The other side (usually an expense placeholder)
                # We leave the amount empty so Beancount can balance it automatically
                txn.postings.append(
                    data.Posting("Expenses:Unknown", None, 
                               None, None, None, None)
                )

                entries.append(txn)
                
        return entries
```

## Extracting Data from PDFs

Sometimes banks only provide PDF statements. Extracting data from them is harder but possible using tools like `pdftotext`.

### 1. Install Dependencies
You need the `poppler-utils` package installed on your system.

*   **Debian/Ubuntu**: `sudo apt install poppler-utils`
*   **macOS**: `brew install poppler`
*   **Nix**: `nix-env -iA nixpkgs.poppler_utils`

### 2. Calling `pdftotext`
Use Python's `subprocess` to run the command and capture the output. The `-layout` flag is crucial as it preserves the visual structure of the table.

```python
import subprocess

def extract_pdf_text(filepath):
    # Run pdftotext -layout input.pdf -
    # The '-' at the end tells it to print to stdout
    result = subprocess.run(
        ['pdftotext', '-layout', filepath, '-'], 
        stdout=subprocess.PIPE, 
        text=True
    )
    return result.stdout
```

### 3. Parsing the Output
Once you have the text string, you iterate over the lines and use Regular Expressions (Regex) to find transaction rows.

```python
import re

# Example Regex for a line like: "Jan 01   Grocery Store   -50.00"
# (\w+ \d+)   : Date (Group 1)
# \s+         : Spaces
# (.*?)       : Description (Group 2)
# \s+         : Spaces
# (-?[\d\.]+) : Amount (Group 3)
TXN_REGEX = re.compile(r'(\w+ \d+)\s+(.*?)\s+(-?[\d\.]+)')

def parse_text(text):
    for line in text.splitlines():
        match = TXN_REGEX.search(line)
        if match:
            date_str, desc, amount_str = match.groups()
            # ... create transaction object ...
```

## Running Your Import

Create a configuration script (e.g., `import.py`) to run your importers.

```python
from beangulp import Ingest
import my_importers

ingest = Ingest()
ingest.add(my_importers.MyBankImporter("Assets:Bank:Checking"))

if __name__ == "__main__":
    ingest()
```

You can now run this script to generate beancount entries:

```bash
python3 import.py /path/to/downloaded.csv
```

## Best Practices
- **Use `beangulp.testing`**: Beangulp includes utilities to test your importers against sample files.
- **Smart Categorization**: You can add logic in `extract` to guess the expense account based on the description (e.g., if "Coffee" in description, use `Expenses:Food:Coffee`).
