# Standard Plugins

Beancount includes several plugins that automate common tasks. To use them, add `plugin "..."` to your file.

## `beancount.plugins.auto_accounts`
**Description**: Automatically creates `open` directives for accounts that are referenced but not defined.
**Usage**: Great for lazy prototyping, but discouraged for strict accounting.

```beancount
plugin "beancount.plugins.auto_accounts"
```

## `beancount.plugins.tag_pending`
**Description**: Automatically adds a `#pending` tag to any transaction marked with the `!` flag.
**Usage**: clear visualization of what is not yet cleared.

```beancount
plugin "beancount.plugins.tag_pending"
```

## `beancount.plugins.forecast`
**Description**: Generates recurring transactions into the future for budgeting/planning.
**Usage**: You define a "forecast" transaction, and the plugin projects it forward.

## `beancount.plugins.check_commodity`
**Description**: Ensures that all commodities used in an account match the `open` directive restrictions.
**Usage**: Strict validation.

```beancount
2000-01-01 open Assets:Bank:US USD
; This will fail if you try to put EUR in it
```
