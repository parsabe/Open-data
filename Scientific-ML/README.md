
# Scientific ML

**Overview:**

A lightweight end-to-end BTC pipeline to forecast next-day Bitcoin returns and evaluate a simple trading strategy.

## Requirements

* Python 3.7+
* pandas, numpy
* scikit-learn
* xgboost, lightgbm, catboost
* pysindy

Install dependencies:

```bash
pip install -r requirements.txt
```

## Usage

1. Open the Jupyter notebook `main.ipynb` in the `docs/` directory.
2. Review the theoretical explanations and code cells for each step.
3. Execute all cells sequentially (Shift + Enter) to reproduce the pipeline:
   * Data loading & cleaning
   * SINDy modeling and feature creation
   * Model training, tuning, and ensembling
   * Backtesting and trade log generation
4. Find outputs in the notebook:
   * Plots and metrics displayed inline
   * `trade_log.csv` saved to disk

## Pipeline Steps

1. **Load & clean data** : compute returns, moving averages.
2. **SINDy modeling** : extract linear dynamics and create rate features.
3. **Feature engineering** : raw OHLCV, technical indicators, SINDy rates.
4. **Model training** : time-series CV tuning of XGBoost, LightGBM, CatBoost.
5. **Ensemble & predict** : average model outputs, convert to simple returns.
6. **Backtest** : generate signals, apply transaction costs, save trade log.

## License

MIT License
