# mini-backtester

A small **event-driven backtest engine** for crypto strategies, built on public Binance
OHLCV data. It walks candles one bar at a time through a strategy, simulates fills, and
reports performance metrics (PnL, drawdown, Sharpe, win rate).

This is a learning / portfolio project — a simplified, public-data version of the kind of
backtesting infrastructure I build for live trading research.

## Why event-driven?

Vectorized backtests are fast but quietly leak future information. An event-driven loop
processes one candle at a time, so the strategy only ever sees data it would have had
live — which makes results far closer to reality.

## Install

```bash
git clone https://github.com/alexey-chechikov/mini-backtester
cd mini-backtester
pip install -r requirements.txt
```

## Quick start

```bash
# 1. Download public historical candles from Binance
python -m mini_backtester.data --symbol BTCUSDT --interval 1h --days 365

# 2. Run a backtest with the built-in SMA-crossover strategy
python -m mini_backtester.run --symbol BTCUSDT --interval 1h --fast 20 --slow 50
```

Example output:

```
=== Backtest report: BTCUSDT 1h ===
Period:        2024-05-01 -> 2025-05-01  (8760 bars)
Strategy:      SMA crossover (fast=20, slow=50)
Trades:        48
Win rate:      41.7%
Total return:  +12.3%
Max drawdown:  -8.6%
Sharpe (ann.): 1.04
```

## Project layout

```
mini_backtester/
  data.py        # download & cache public Binance OHLCV
  engine.py      # event-driven backtest loop + fill simulation
  strategy.py    # Strategy base class + SMA-crossover example
  metrics.py     # PnL, drawdown, Sharpe, win rate
  run.py         # CLI entry point
```

## Writing your own strategy

Subclass `Strategy` and implement `on_bar`:

```python
from mini_backtester.strategy import Strategy

class MyStrategy(Strategy):
    def on_bar(self, bar, position):
        if some_condition(bar):
            return "BUY"
        if other_condition(bar):
            return "SELL"
        return None  # hold
```

## Notes & limitations

- Single asset, long/flat only — kept intentionally simple.
- Fills are simulated at the next bar's open with a configurable fee.
- Not financial advice; for research and educational use.

## License

MIT
