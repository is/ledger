# AGENTS.md - Beancount Gold Trading Ledger

## Project Overview

This is a Beancount double-entry accounting ledger project for tracking gold trading transactions. The primary ledger file is `gold_trading.bean`.

## Environment

- Python 3.12.3
- Beancount 3.2.0
- Virtual environment: `.venv/`

## Build/Lint/Test Commands

### Validate Ledger
```bash
.venv/bin/bean-check gold_trading.bean
```
Validates the beancount ledger file for syntax errors, unbalanced transactions, and other issues.

### Format Ledger
```bash
.venv/bin/bean-format gold_trading.bean
```
Formats the ledger file according to beancount conventions.

### Query Ledger
```bash
.venv/bin/bean-query gold_trading.bean
```
Interactive query shell for the ledger.

### Doctor (Debugging)
```bash
.venv/bin/bean-doctor gold_trading.bean
```
Provides debugging and diagnostic information.

### Single Test
Beancount does not have traditional unit tests. Validation is done by running `bean-check` on the ledger file.

## Code Style Guidelines

### File Structure
- Ledger files use `.bean` extension
- Main entry point: `gold_trading.bean`
- Use `;` for comments
- Blank lines separate directives

### Naming Conventions

**Accounts**
- Use PascalCase with colons for hierarchy: `Assets:Bank`, `Expenses:Gold`
- Standard top-level accounts: Assets, Liabilities, Equity, Income, Expenses
- Sub-accounts describe specific holdings or categories

**Currencies**
- Use ISO 4217 codes: `CNY`, `USD`, `GOLD`
- Commodities (stocks, metals) use ticker-like symbols

**Commodities**
- Define with `commodity` directive
- Include metadata: `name: "description"`

### Date Format
- Use ISO format: `YYYY-MM-DD`
- Transaction dates must be chronological

### Transactions
```
YYYY-MM-DD txn "Description"
  Account1         -100.00 CNY
  Account2          100.00 CNY
```
- Description in double quotes
- Indent posting amounts (positive = debit, negative = credit)
- Use `@@` for total amount on single line: `81.0004 GOLD @@ 94034.77 CNY`

### Prices
- Define with `price` directive: `2026-02-02 price GOLD 1160.92 CNY`

### Opening Accounts
```
YYYY-MM-MM open AccountName CURRENCY
```
- Use future-dated open for new accounts
- Include all currencies that may be used

### Options
- Set at top of file: `option "operating_currency" "CNY"`

### Error Handling
- Always run `bean-check` after modifications
- Fix unbalanced transactions immediately
- Verify price directives match transaction dates

### Best Practices
1. Always validate with `bean-check` before committing
2. Use `bean-format` to maintain consistent formatting
3. Include clear transaction descriptions
4. Record prices on transaction dates
5. Keep commodities defined before first usage
6. Use 2 decimal places for currencies, 4+ for commodities

## Common Tasks

### Add New Transaction
1. Add transaction in chronological order
2. Ensure debits equal credits
3. Run `bean-check` to validate

### Add New Account
1. Add `open` directive before first usage
2. Include expected currencies

### Change Currency
1. Add new `open` directive with new currency
2. Migrate existing postings

## Notes

- This is a data-driven project (not code)
- No CI/CD pipelines, linters, or test frameworks beyond beancount validation
- All "code" is the beancount ledger format itself

## Common Transaction Patterns

### Buy Gold
```
YYYY-MM-DD txn "买入黄金"
  Assets:Bank        -94034.77 CNY
  Assets:Gold       81.0004 GOLD @@ 94034.77 CNY
```

### Sell Gold
```
YYYY-MM-DD txn "卖出黄金"
  Assets:Gold       -60.0000 GOLD @@ 58847.40 CNY
  Assets:Bank       58847.40 CNY
```

### Bank Transfer
```
YYYY-MM-DD txn "转账"
  Assets:Bank       -5000.00 CNY
  Assets:Bank:ABC    5000.00 CNY
```

### Opening Balance
```
YYYY-MM-DD txn "初始余额"
  Assets:Bank        100000.00 CNY
  Equity:Opening-Balances  -100000.00 CNY
```

## Troubleshooting

### Unbalanced Transaction
If `bean-check` reports unbalanced transactions, verify:
1. All postings sum to zero
2. No missing postings
3. Currency amounts are correct

### Missing Price
If a commodity price is needed but not defined:
1. Add a `price` directive for the date of the transaction
2. Prices should be defined chronologically before transactions that need them

### Account Not Opened
If an account is not recognized:
1. Add an `open` directive before the transaction
2. Include all currencies that will be used in that account
