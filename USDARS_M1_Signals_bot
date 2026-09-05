# USDARS_M1_Signals_bot.py

import pandas as pd
import numpy as np

def calculate_ema(series, period):
    return series.ewm(span=period, adjust=False).mean()

def calculate_rsi(close, period=14):
    delta = close.diff()

    gain = delta.clip(lower=0)
    loss = -delta.clip(upper=0)

    avg_gain = gain.ewm(alpha=1/period, adjust=False).mean()
    avg_loss = loss.ewm(alpha=1/period, adjust=False).mean()

    rs = avg_gain / avg_loss.replace(0, np.nan)
    return 100 - (100 / (1 + rs))


def analyze(data):
    """
    data columns:
    open, high, low, close
    """

    df = pd.DataFrame(data).copy()

    if len(df) < 50:
        return "NO SIGNAL"

    df["ema9"] = calculate_ema(df["close"], 9)
    df["ema21"] = calculate_ema(df["close"], 21)
    df["rsi"] = calculate_rsi(df["close"], 14)

    last = df.iloc[-1]
    previous = df.iloc[-2]

    bullish_cross = (
        previous["ema9"] <= previous["ema21"]
        and last["ema9"] > last["ema21"]
    )

    bearish_cross = (
        previous["ema9"] >= previous["ema21"]
        and last["ema9"] < last["ema21"]
    )

    # CALL
    if bullish_cross and 50 < last["rsi"] < 70:
        return "CALL"

    # PUT
    if bearish_cross and 30 < last["rsi"] < 50:
        return "PUT"

    return "NO SIGNAL"


# تجربة على بيانات شموع
if __name__ == "__main__":

    # مثال بيانات تجريبية
    np.random.seed(1)

    prices = 100 + np.cumsum(np.random.randn(100) * 0.1)

    data = {
        "open": prices,
        "high": prices + 0.05,
        "low": prices - 0.05,
        "close": prices
    }

    signal = analyze(data)

    print("USDARS M1 SIGNAL:", signal)
