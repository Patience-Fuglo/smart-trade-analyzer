# 📊 Smart Trade Analyzer (Console-Based)

A console-based trade analysis engine built with Python OOP — models real financial trade data, computes P&L, classifies outcomes, and applies strategy filters using lambdas and inheritance. Designed to reflect how actual trading systems are structured.

---

## 🧩 Project Overview

Trading systems at financial firms don't just store data — they model it. A raw tuple like `('TSLA', 2800, 2950, 4)` needs to become a structured object that knows its own profit, status, and how it ranks against other trades. This project builds exactly that kind of system from scratch.

The design uses a **three-class hierarchy**: a `Trade` object that owns its own calculations, a `TradeAnalyzer` that aggregates across a portfolio, and a `StrategyAnalyzer` subclass that adds lambda-based filtering for signal generation. The entry point follows the `if __name__ == "__main__"` pattern, making it runnable as a standalone script.

---

## 🎯 What It Does

Given a list of market trade tuples:

```python
('SYMBOL', buy_price, sell_price, quantity)
```

The system:

1. **Validates** inputs at construction time — raises `ValueError` for non-positive prices or quantities
2. **Calculates** Profit/Loss per trade: `(sell − buy) × quantity`
3. **Classifies** trade outcome: `PROFIT`, `LOSS`, or `BREAKEVEN`
4. **Aggregates** portfolio metrics: total P&L, best and worst trades
5. **Filters** trades by strategy thresholds using `lambda` + `filter()`
6. **Prints** a formatted trade summary to the console

---

## 🗂️ Project Structure

```
smart-trade-analyzer/
│
├── Smart_Trade_Analyzer_Console_Based.ipynb
└── README.md
```

---

## 🏗️ Class Design

```
Trade
 ├── __init__()         → validates and stores symbol, prices, quantity
 ├── profit()           → (sell_price - buy_price) * quantity
 ├── status()           → PROFIT / LOSS / BREAKEVEN
 └── __str__()          → formatted string representation

TradeAnalyzer
 ├── add_trade()        → appends Trade object to internal list
 ├── total_profit()     → sums profit() across all trades
 ├── best_trade()       → max() with lambda key on profit()
 ├── worst_trade()      → min() with lambda key on profit()
 └── summary()         → prints full formatted report

StrategyAnalyzer(TradeAnalyzer)       ← inherits all analyzer logic
 ├── profitable_trades(min_profit)    → filter() trades above threshold
 └── loss_trades(max_loss)            → filter() trades below threshold
```

---

## ▶️ Sample Run

**Input market data:**
```python
market_data = [
    ('AAPL',  150,  160, 10),
    ('GOOGL', 720,  690,  5),
    ('MSFT',  100,  100,  8),
    ('TSLA', 2800, 2950,  4),
    ('AMZN', 1800, 1750,  6),
    ('NTLX',  342,  400,  5)
]
```

**Console output:**
```
------------------------------
Trade Summary
------------------------------
AAPL  | QTY: 10 | P/L:  100 | Status: PROFIT
GOOGL | QTY:  5 | P/L: -150 | Status: LOSS
MSFT  | QTY:  8 | P/L:    0 | Status: BREAKEVEN
TSLA  | QTY:  4 | P/L:  600 | Status: PROFIT
AMZN  | QTY:  6 | P/L: -300 | Status: LOSS
NTLX  | QTY:  5 | P/L:  290 | Status: PROFIT
------------------------------
Total Profit: 540
Best Trade:   TSLA | QTY: 4 | P/L: 600 | Status: PROFIT
Worst Trade:  AMZN | QTY: 6 | P/L: -300 | Status: LOSS
------------------------------

Profitable Trades (≥ $100 P/L):
AAPL  | QTY: 10 | P/L:  100 | Status: PROFIT
TSLA  | QTY:  4 | P/L:  600 | Status: PROFIT
NTLX  | QTY:  5 | P/L:  290 | Status: PROFIT

Lossy Trades (≤ -$100 P/L):
GOOGL | QTY:  5 | P/L: -150 | Status: LOSS
AMZN  | QTY:  6 | P/L: -300 | Status: LOSS
```

---

## 🛡️ Validation & Error Handling

Validation is enforced at the point of object creation — invalid data never enters the portfolio:

```python
# Both raise ValueError before the object is created
Trade('FAKE', -50, 100, 10)   # buy_price <= 0
Trade('FAKE', 100, 200, 0)    # quantity <= 0
```

This is an intentional design choice: **fail fast at the boundary**, not silently later during analysis. In a real trading system, you'd wrap `Trade(*data)` in a `try/except` at the ingestion layer and log the rejection — exactly the pattern used in Projects 1 and 2.

---

## 💡 Key Design Decisions

**Validation in `__init__`, not in a helper** — keeping the guard inside the constructor means a `Trade` object is always in a valid state. There's no way to create a `Trade` with a zero quantity and accidentally include it in a summary.

**`profit()` as a method, not a stored attribute** — profit is derived from prices and quantity, both of which are fixed at construction. Computing it on demand avoids the risk of a stale cached value if attributes were ever mutated.

**`best_trade()` and `worst_trade()` use `max()`/`min()` with lambda keys** — the commented-out manual loop versions are left in the notebook deliberately, showing the progression from imperative to functional style. The lambda version is more Pythonic and handles any list size without extra bookkeeping.

**`StrategyAnalyzer` inherits rather than composes** — it is-a `TradeAnalyzer` with additional filtering capability. It gets `add_trade()`, `total_profit()`, `best_trade()`, `worst_trade()`, and `summary()` for free, and the `main()` function only ever instantiates `StrategyAnalyzer`.

**`filter()` with lambdas over list comprehensions** — both approaches are shown in the notebook comments. `filter(lambda t: t.profit() >= min_profit, self.trades)` makes the intent explicit: this is a filter operation, not a transformation, which matters when reading strategy logic quickly.

**`if __name__ == "__main__"`** — the entry point guard means the module can be imported without side effects, a standard Python pattern that makes the code testable and reusable as a library.

---

## 🧠 Concepts Demonstrated

| Concept | Where Applied |
|---|---|
| OOP — encapsulation | `Trade` owns its own `profit()` and `status()` logic |
| OOP — inheritance | `StrategyAnalyzer` extends `TradeAnalyzer` |
| `__str__` dunder method | Custom string representation for `Trade` objects |
| Lambda functions | `max()`, `min()`, and `filter()` key functions |
| `filter()` built-in | Strategy-based trade screening |
| Argument unpacking (`*data`) | Clean trade construction from tuples |
| Guard clauses | Price and quantity validation in `__init__` |
| `if __name__ == "__main__"` | Script entry point pattern |
| Functional vs imperative | Both loop and lambda approaches shown side-by-side |

---

## 🚀 How to Run

No dependencies — pure Python 3.

```bash
# Clone the repo
git clone https://github.com/Patience-Fuglo/smart-trade-analyzer.git
cd smart-trade-analyzer

# Option 1: Run as a script (if converted to .py)
python smart_trade_analyzer.py

# Option 2: Open in Jupyter or Google Colab and run all cells
```

---

## 📌 Context

Third project in an intensive Python for Finance series. Where Projects 1 and 2 focused on functional pipelines and defensive data handling, this project introduces a proper OOP class hierarchy with inheritance — the structural pattern used in real trading and risk management systems. The domain is deliberately financial: profit/loss calculation, strategy thresholding, and best/worst trade identification are all core to how quant and trading desks think about position analysis.
