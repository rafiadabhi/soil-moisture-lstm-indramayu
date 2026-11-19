# Soil Moisture Forecasting in Indramayu: SARIMA-GARCH vs LSTM

Comparative study of a hybrid statistical model (**SARIMA-GARCH**) and a deep learning model (**LSTM Seq2Seq**) for forecasting weekly soil moisture in Indramayu Regency, West Java, Indonesia, using 2014–2023 satellite data.

## Results

Both models were trained on 2014–2022 and evaluated on the same 52-week test period (January–December 2023).

| Model | RMSE | MAPE (%) |
|---|---|---|
| SARIMA(2,0,2)(0,1,1)[52] – GARCH(1,1) Student-t | 0.0349 | 8.47 |
| **LSTM Seq2Seq** | **0.0306** | **7.08** |

- The LSTM achieved lower error on both metrics. Across three random seeds, LSTM test RMSE ranged from 0.0305 to 0.0346, all below SARIMA-GARCH.
- The GARCH component does not change the SARIMA point forecast, but it improved the 95% prediction interval coverage from 92.3% to 98.1%.

## Data

- **Source:** ESA CCI Soil Moisture (combined active–passive product), accessed via the CEDA Archive.
- **Area:** Indramayu Regency (6.25°–6.67° S; 107.87°–108.60° E), averaged over all pixels.
- **Period:** 1 January 2014 – 31 December 2023, aggregated to weekly means (522 observations).
- **Unit:** volumetric soil moisture (m³/m³).

## Methodology

**SARIMA-GARCH**
- Box–Cox power transformation (λ = 2) and seasonal differencing (s = 52).
- Model identification with ACF, PACF, and EACF; selection by AIC/BIC and parameter significance, followed by overfitting checks.
- Residual diagnostics (Shapiro–Wilk, Ljung–Box at lags 10/20/52, t-test) and ARCH-LM test.
- GARCH(p,q) with normal and Student-t errors fitted on SARIMA residuals; parameter significance based on robust standard errors.
- Back-transformation with second-order bias correction; evaluation of point forecasts and 95% prediction intervals.

**LSTM Seq2Seq**
- Encoder–decoder LSTM (64 units each) with a 52-week input window and 52-week output horizon.
- Standardization fitted on training data only; training windows never overlap the evaluation period.
- Grid search over batch size {16, 32, 64}, epochs {50, 100}, and dropout {0.05, 0.1}, selected on a 2022 validation set.
- Each configuration trained with three random seeds; the final model is retrained on 2014–2022 and the test forecast is the average across seeds.

## Repository Structure

```
├── data/
│   └── SM_Avg_Weekly_Indramayu_2014_2023.xlsx    # weekly soil moisture series
├── notebook/
│   ├── lstm-indramayu-sm-2014-2023.ipynb         # LSTM Seq2Seq (Python, Kaggle)
│   └── SARIMA_GARCH_SM_Indramayu_final.html      # SARIMA-GARCH report (R Markdown output)
├── paper/
│   └── PAPER_MPDW_final.pdf                      # full paper
├── ppt/
├── LICENSE
└── README.md
```

## Reproducing the LSTM Notebook

The notebook was developed on Kaggle (TensorFlow/Keras). It looks for the data file under `/kaggle/input`; if not found, it downloads the file from this repository's `data/` folder (internet access required). Outputs (metrics, forecasts, figures) are saved to `/kaggle/working`.

## Authors

Rafi Adabhi Sunarya, Ibrahim Zaidan, Aurisa Putri Hawin Yusima, Qaulan Sadiida Putri Sumarna, Yenni Angraini, Windy Pangesti

Department of Statistics, IPB University, Bogor, Indonesia

## License

This project is licensed under the [MIT License](LICENSE).
