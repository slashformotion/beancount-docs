# How to Fetch Market Prices

To track the value of your investments (Stocks, ETFs, Crypto) in Beancount, you need to record their market price over time.

## The `price` Directive
Manually, you can add prices like this:

```beancount
2024-01-01 price AAPL 150.00 USD
2024-01-02 price AAPL 152.00 USD
```

But doing this manually is tedious.

## Using `bean-price`
Beancount comes with a tool called `bean-price` that fetches prices from the internet.

### 1. Install Requirements
You might need to install extra sources:
```bash
pip install beancount-beanprice
```

### 2. Configure Your Commodities
You need to tell `bean-price` where to look for the price. You do this by adding metadata to your `commodity` directive.

> **Note:** The `commodity` directive is optional in basic Beancount but required if you want to attach metadata like this. See [Syntax Reference](../reference/syntax.md#5-commodity).

```beancount
1980-01-01 commodity AAPL
  price: "Yahoo/AAPL"
  name: "Apple Inc."

1980-01-01 commodity BTC
  price: "Coinbase/BTC-USD"
```

### 3. Run the Tool
Run `bean-price` against your ledger file. It will:
1.  Find all commodities with a `price` source.
2.  Find the last date they were updated.
3.  Fetch the current price.

```bash
bean-price main.beancount
```

### 4. Save the Prices
Usually, you append the output to a separate prices file (e.g., `prices.beancount`) that you include in your main file.

**In `main.beancount`:**
```beancount
include "prices.beancount"
```

**Run command:**
```bash
bean-price -e USD main.beancount >> prices.beancount
```
