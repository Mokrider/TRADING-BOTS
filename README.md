# TRADING-BOTS
Here are one of the top EA of all the time
rsi_trading_bot.py
import pandas as pd
import yfinance as yf

# SETTINGS
SYMBOL = "EURUSD=X"   # Forex pair
TIMEFRAME = "1h"
RSI_PERIOD = 14

# FETCH DATA
def get_data(symbol, interval):
    data = yf.download(symbol, period="7d", interval=interval)
    return data

# CALCULATE RSI
def calculate_rsi(data, period=14):
    delta = data['Close'].diff()

    gain = (delta.where(delta > 0, 0)).rolling(window=period).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()

    rs = gain / loss
    rsi = 100 - (100 / (1 + rs))

    return rsi

# TRADING LOGIC
def generate_signal(data):
    data['RSI'] = calculate_rsi(data)

    latest_rsi = data['RSI'].iloc[-1]

    if latest_rsi < 30:
        return "BUY"
    elif latest_rsi > 70:
        return "SELL"
    else:
        return "HOLD"

# MAIN FUNCTION
def run_bot():
    print("Fetching market data...")
    data = get_data(SYMBOL, TIMEFRAME)

    signal = generate_signal(data)

    print(f"Latest Signal for {SYMBOL}: {signal}")

    if signal == "BUY":
        print("→ Market is oversold. Potential buying opportunity.")
    elif signal == "SELL":
        print("→ Market is overbought. Potential selling opportunity.")
    else:
        print("→ No clear signal. Stay out of the market.")

if __name__ == "__main__":
    run_bot()
