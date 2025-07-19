# AI Crypto Trading Bot

An intelligent, modular cryptocurrency trading bot that leverages technical analysis and real-time social signals, using either **Google Gemini** or **OpenAI's GPT** as its decision-making brain. This bot is designed to trade DOGE-USD on the Coinbase Advanced Trade platform.

This project was built to be robust, secure, and easily extensible—perfect for a hackathon or as a foundation for your own trading experiments.

## Features

- **Modular Architecture**: Cleanly separated logic for API clients, trading strategy, and bot execution.
- **Dual AI Support**: Easily switch between Google Gemini and OpenAI GPT-4 for analysis.
- **Technical Analysis Strategy**: Incorporates standard trading indicators like **RSI** (Relative Strength Index) and **MACD** (Moving Average Convergence Divergence) for data-driven signals.
- **Real-time Social Signals**: Monitors Elon Musk's Twitter (X) feed for potential market-moving announcements about Dogecoin.
- **Secure API Key Management**: Uses a `.env` file to keep your sensitive API keys safe and out of the codebase.
- **Live Trading**: Connects directly to the Coinbase Advanced Trade API to execute real market orders.
- **Stateful Position Tracking**: The bot knows whether it currently holds an asset to avoid making redundant trades (e.g., buying twice).

## Architecture Overview

The program is broken down into several key modules:

- **`main.py`**: The entry point to start the bot.
- **`bot.py`**: The core application logic. It orchestrates the data gathering, AI consultation, and trade execution loop.
- **`api_clients.py`**: Handles all communication with external services (Coinbase for market data/trading and Tweepy for Twitter).
- **`strategy.py`**: Contains the technical analysis logic. It takes raw market data and generates a preliminary "BUY", "SELL", or "HOLD" signal.
- **`config.py` & `.env`**: Manages the secure loading of API keys and other configuration variables.

```
[Coinbase API] <--> [api_clients.py] --(Market Data)--> [strategy.py] --(Tech Signal)--> |
                                                                                      |
 [Twitter API]  <--> [api_clients.py] --(Tweet Text) ---> [    bot.py   ] --(Execute)--> [api_clients.py] --> [Coinbase API]
                                                                                      ^
                                                                                      |
[Google Gemini / OpenAI API] <---(Prompt)------------- [    bot.py   ] --(Decision)----|
```

---

## Getting Started

Follow these steps to get the bot up and running on your local machine.

### 1. Prerequisites

- Python 3.8 or newer
- Git
- API keys for:
  - **Coinbase Advanced Trade**
  - **Twitter (X) Developer Account** (for a Bearer Token)
  - **Google AI Studio** (for a Gemini API Key) OR **OpenAI**

### 2. Installation

First, clone the repository to your local machine:
```bash
git clone https://github.com/your-username/ai-crypto-bot.git
cd ai-crypto-bot
```

Next, create a virtual environment to keep dependencies isolated. This is highly recommended.
```bash
# For Mac/Linux
python3 -m venv venv
source venv/bin/activate

# For Windows
python -m venv venv
.\venv\Scripts\activate
```

Install all the required Python packages:
```bash
pip install -r requirements.txt
```

### 3. Configuration

The bot requires API keys to function.

1.  Create a file named `.env` in the root of the project folder.
2.  Copy the contents of the example below into your new `.env` file.
3.  Replace the placeholder text with your actual API keys.

** Important**: The `.env` file contains sensitive information. **Never commit it to GitHub!** The `.gitignore` file should already be configured to ignore it.

**`.env` file contents:**
```
# Coinbase API Keys (ensure your key has trading permissions)
COINBASE_API_KEY="YOUR_COINBASE_API_KEY"
COINBASE_API_SECRET="YOUR_COINBASE_API_SECRET"

# Twitter (X) API Bearer Token
TWITTER_BEARER_TOKEN="YOUR_TWITTER_BEARER_TOKEN"

# Google Gemini API Key
GOOGLE_API_KEY="YOUR_GEMINI_API_KEY"

# --- OR ---

# OpenAI API Key (only needed if you switch to OpenAI)
# OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

---

##  How to Run the Bot

Once your `.env` file is configured, you can start the bot with a single command:

```bash
python main.py
```

The bot will start its loop, printing logs to the console with its analysis, AI decisions, and any trades it makes.

To stop the bot, press `Ctrl + C` in the terminal.

---

## 🛠️ Customization

### Changing the Cryptocurrency

To trade a different crypto pair, simply change the `product_id` in `main.py`:

```python
# main.py
if __name__ == "__main__":
    # Change "DOGE-USD" to something else like "BTC-USD" or "ETH-USD"
    bot = TradingBot(product_id="BTC-USD", trade_size_quote=10.0)
    bot.run()
```

### Adjusting the Trading Strategy

You can easily tweak the technical analysis parameters in `strategy.py`. For example, change the RSI thresholds for buying or selling:

```python
# strategy.py -> get_technical_signals()
# Change RSI thresholds from 35/65 to 30/70 for a less frequent signal
if signals['rsi'] < 30 and ...:
    signals['decision'] = 'BUY'
if signals['rsi'] > 70 or ...:
    signals['decision'] = 'SELL'
```

### Switching to OpenAI

This bot is configured for Google Gemini by default. To switch to OpenAI's GPT-4:

1.  Make sure you have your `OPENAI_API_KEY` in the `.env` file.
2.  Go to `bot.py` and make the following changes:
    - Comment out the Google Gemini imports and client setup.
    - Uncomment the OpenAI imports and client setup.
    - The `ask_ai` function will need to be swapped with the OpenAI-compatible version (which uses `system` and `user` roles). An example is provided in the `bot.py` comments.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
