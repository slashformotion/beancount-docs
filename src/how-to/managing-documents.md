# How to Manage Documents

Beancount allows you to link external files (PDFs, images) to specific transactions. This is invaluable for auditing and keeping track of receipts.

## The `document` directive

To link a file to an account (without a specific transaction context), use the `document` directive.

```beancount
2024-03-15 document Liabilities:CreditCard "/path/to/statement_mar2024.pdf"
```

## Linking to Transactions

Beancount automatically associates documents with transactions if the date and tags match, but the most robust way is to use the **metadata** field `statement` or simply let [Fava](using-fava.md) handle the discovery based on file location.

However, the standard convention in Beancount 3 is to use the `document` directive to declare the file exists, and then standard tooling (like [Fava](using-fava.md)) displays it.

### Organizing Files
Beancount enforces a specific directory structure for documents if you want automatic discovery:

```text
/path/to/documents/
  Liabilities/
    CreditCard/
      2024-03-15.Statement.pdf
```

1.  Set the `documents` option in your file:
    ```beancount
    option "documents" "/path/to/documents"
    ```
2.  Beancount will scan this directory.
3.  Fava will automatically show `2024-03-15.Statement.pdf` next to any transaction involving `Liabilities:CreditCard` on that date.

## Best Practices

*   **Scans**: Scan all physical receipts immediately.
*   **Naming**: Use `YYYY-MM-DD.Description.pdf` so they sort naturally in your file explorer.
*   **Privacy**: Remember these files are just on your disk. Beancount doesn't encrypt them.
