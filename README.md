import requests

# Replace with your Alpha Vantage API key
API_KEY = "YOUR_ALPHA_VANTAGE_API_KEY"
BASE_URL = "https://www.alphavantage.co/query"

# Portfolio structure: { "TICKER": {"shares": int, "price": float} }
portfolio = {}

def get_stock_price(symbol):
    params = {
        "function": "GLOBAL_QUOTE",
        "symbol": symbol,
        "apikey": API_KEY
    }
    response = requests.get(BASE_URL, params=params)
    data = response.json()
    
    try:
        price = float(data["Global Quote"]["05. price"])
        return price
    except (KeyError, ValueError):
        print("Error retrieving data for", symbol)
        return None

def add_stock():
    symbol = input("Enter stock ticker (e.g., AAPL): ").upper()
    shares = int(input(f"Enter number of shares of {symbol}: "))
    price = get_stock_price(symbol)

    if price is not None:
        portfolio[symbol] = {"shares": shares, "price": price}
        print(f"Added {shares} shares of {symbol} at ${price:.2f} each.")

def remove_stock():
    symbol = input("Enter stock ticker to remove: ").upper()
    if symbol in portfolio:
        del portfolio[symbol]
        print(f"Removed {symbol} from portfolio.")
    else:
        print(f"{symbol} not found in portfolio.")

def view_portfolio():
    if not portfolio:
        print("Portfolio is empty.")
        return

    print("\nYour Portfolio:")
    total_value = 0.0

    for symbol, data in portfolio.items():
        price = get_stock_price(symbol)
        if price is None:
            continue
        value = data["shares"] * price
        total_value += value
        print(f"{symbol}: {data['shares']} shares @ ${price:.2f} = ${value:.2f}")

    print(f"Total Portfolio Value: ${total_value:.2f}")

def main():
    while True:
        print("\n--- Stock Portfolio Tracker ---")
        print("1. Add Stock")
        print("2. Remove Stock")
        print("3. View Portfolio")
        print("4. Exit")

        choice = input("Enter your choice (1-4): ")

        if choice == "1":
            add_stock()
        elif choice == "2":
            remove_stock()
        elif choice == "3":
            view_portfolio()
        elif choice == "4":
            print("Exiting Portfolio Tracker.")
            break
        else:
            print("Invalid choice. Try again.")

# Run the program
if __name__ == "__main__":
    main()
