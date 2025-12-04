import ccxt
import time

# Set your API credentials (you can get these from your exchange account)
api_key = 'YOUR_API_KEY'
api_secret = 'YOUR_API_SECRET'

# Initialize the exchange (Binance in this example)
exchange = ccxt.binance({
    'apiKey': api_key,
    'secret': api_secret,
})

# Define trading pair and amount
symbol = 'BTC/USDT'  # Example: trading Bitcoin with USDT (Tether)
amount = 0.001  # Amount of BTC to buy/sell

# Function to get current market price of the symbol
def get_current_price():
    ticker = exchange.fetch_ticker(symbol)
    return ticker['last']

# Function to get open positions (this works for spot accounts, check if using margin or futures)
def get_open_positions():
    balance = exchange.fetch_balance()
    return balance['total']

# Function to place a market buy order
def place_buy_order():
    print(f"Placing buy order for {amount} {symbol}")
    order = exchange.create_market_buy_order(symbol, amount)
    print(f"Buy order placed: {order}")

# Function to place a market sell order
def place_sell_order():
    print(f"Placing sell order for {amount} {symbol}")
    order = exchange.create_market_sell_order(symbol, amount)
    print(f"Sell order placed: {order}")

# Function to open a new position (buy or sell based on the price)
def open_position(price_threshold, action='buy'):
    current_price = get_current_price()
    print(f"Current price of {symbol}: ${current_price}")
    
    if action == 'buy' and current_price < price_threshold:
        place_buy_order()
    elif action == 'sell' and current_price > price_threshold:
        place_sell_order()

# Function to close the current position (sell if you bought or buy if you sold)
def close_position():
    positions = get_open_positions()
    if positions['BTC'] > 0:
        place_sell_order()
    elif positions['BTC'] < 0:
        place_buy_order()

# Example strategy: Buy when price is lower than $30,000, Sell when price is higher than $40,000
def trade_strategy():
    while True:
        try:
            current_price = get_current_price()
            print(f"Current price of {symbol}: ${current_price}")

            # Buy condition: price lower than $30,000
            if current_price < 30000:
                open_position(price_threshold=30000, action='buy')

            # Sell condition: price higher than $40,000
            elif current_price > 40000:
                open_position(price_threshold=40000, action='sell')

            time.sleep(60)  # Wait 1 minute before checking again
        except Exception as e:
            print(f"Error: {e}")
            time.sleep(60)  # Wait and retry if error occurs

# Start the trading strategy
trade_strategy()
