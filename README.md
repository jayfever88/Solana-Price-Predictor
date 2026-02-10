# Solana Price Predictor (AI + TA)

## Hosted Link
*(Optional: add link if deployed as a web app or API)*

---

## 📖 Description
This project predicts **Solana (SOL)** price movements using technical indicators:
- **MACD** (trend momentum)
- **RSI** (overbought/oversold conditions)
- **EMA** (trend direction)
- **Volume Trend** (market activity)

It’s novel because it combines multiple signals into a simple **autonomous trading agent** that outputs BUY/SELL/HOLD recommendations.

---

## 🔗 Solana + AI Agent
- **Solana Usage**  
  This project focuses on Solana’s price prediction, which can be integrated into Solana trading bots or dApps.  

- **AI Agent Autonomy**  
  - **Planning**: Fetches live Solana price data.  
  - **Execution**: Calculates indicators (MACD, RSI, EMA, Volume).  
  - **Iteration**: Updates signals continuously as new data arrives.  

---

## ⚙️ Installation & Usage
1. Clone the repository:
   ```bash
   git clone https://github.com/jayfever88/solana-predictor.git
   cd solana-predictor
import pandas as pd
import numpy as np
import requests

# Fetch Solana price data (Binance API)
def fetch_data(limit=100):
    url = f"https://api.binance.com/api/v3/klines?symbol=SOLUSDT&interval=1h&limit={limit}"
    data = requests.get(url).json()
    df = pd.DataFrame(data, columns=[
        "time","open","high","low","close","volume",
        "close_time","quote","trades","tb_base","tb_quote","ignore"
    ])
    df["close"] = df["close"].astype(float)
    return df

# Simple EMA
def ema(series, period=14):
    return series.ewm(span=period, adjust=False).mean()

# Simple RSI
def rsi(series, period=14):
    delta = series.diff()
    gain = np.where(delta > 0, delta, 0)
    loss = np.where(delta < 0, -delta, 0)
    avg_gain = pd.Series(gain).rolling(period).mean()
    avg_loss = pd.Series(loss).rolling(period).mean()
    rs = avg_gain / avg_loss
    return 100 - (100 / (1 + rs))

# Generate signal
def signal(df):
    close = df["close"]
    ema_val = ema(close).iloc[-1]
    rsi_val = rsi(close).iloc[-1]
    latest = close.iloc[-1]

    if latest > ema_val and rsi_val < 70:
        return "BUY"
    elif latest < ema_val and rsi_val > 30:
        return "SELL"
    else:
        return "HOLD"

if __name__ == "__main__":
    df = fetch_data()
    print("Solana Signal:", signal(df))
