# bitcoin-arbitrage - opportunity detector and automated trading

It gets order books from supported exchanges and calculate arbitrage
opportunities between each markets. It takes market depth into account.
This fork (by bearishtrader) has added various altcoin exchanges and 
trading for altcoin crosses against BTC (such as quarkcoin, dogecoin 
and maxcoin etc.).

Currently supported exchanges to get data:
 - MtGox (USD, EUR)
 - Bitstamp (USD, ~EUR)
 - Bitcoin24 (EUR)
 - Bitfloor (USD)
 - Bitcoin-Central (EUR)
 - BTC-e (USD, EUR)
 - Intersango (EUR)
 - Bitfinex (USD)
 - Kraken (USD, EUR)
 - Bitcoin-Central (EUR)
 - Cryptsy (BTC)
 - Coins-E (BTC)
 - Vircurex (BTC)
 - McxNow (BTC)

Currently supported exchanges to automate trade:
 - MtGox (EUR, USD)
 - Bitstamp (USD)
 - Bitcoin-Central (EUR) - (API changed)
 - Cryptsy (BTC)
 - Coins-E (BTC)
 - Vircurex (BTC)
 - McxNow (BTC)

Donation are always welcome: **1Maxime7WnLqq24hasMA872JZ4VBGMDbKk**

# WARNING

**Real trading bots are included. Don't put your API keys in config.py
  if you don't know what you are doing.  Use TraderBotAltCoin observer
  if you want to trade altcoins like quakrcoin, dogecoin or maxcoin.**

# Installation And Configuration

    cp arbitrage/config.py-example arbitrage/config.py

Then edit config.py file to setup your preferences: watched markets
and observers

You need Python3 to run this program. To install on Debian, Ubuntu, or
variants of them, use:

    $ sudo apt-get install python3
    $ sudo apt-get install python3-setuptools
    $ sudo easy_install3 pip
    $ sudo pip-3.2 install requests nose

To use the observer XMPPMessager you will need to install sleekxmpp:

    $ pip-3.2 install sleekxmpp

To trade on the McxNow exchange you will need to get the mcxnowapi for python 3.2 and install it:

    $ git clone https://github.com/bearishtrader/mcxnowapi.git
    $ cd mcxnowapi
    $ python3 setup.py install

# Run

To run the opportunity watcher:

    $ python3 arbitrage/arbitrage.py
    2013-03-12 03:52:14,341 [INFO] profit: 30.539722 EUR with volume: 10 BTC - buy at 29.3410 (MtGoxEUR) sell at 29.4670 (Bitcoin24EUR) ~10.41%
    2013-03-12 03:52:14,356 [INFO] profit: 66.283642 EUR with volume: 10 BTC - buy at 29.3410 (MtGoxEUR) sell at 30.0000 (BitcoinCentralEUR) ~22.59%
    2013-03-12 03:52:14,357 [INFO] profit: 31.811390 EUR with volume: 10 BTC - buy at 29.3410 (MtGoxEUR) sell at 30.0000 (IntersangoEUR) ~10.84%
    2013-03-12 03:52:45,090 [INFO] profit: 9.774324 EUR with volume: 10 BTC - buy at 35.3630 (Bitcoin24EUR) sell at 35.4300 (BitcoinCentralEUR) ~2.76%

Note, this example is real, it has happened when the blockchain
forked. MtGox is a very reactive market, price dropped significally in
1 hour, this kind of situation opens very good arbitrage
opportunities with slower exchanges.

To check your balance on an exchange (also a good way to check your accounts configuration):

    $ python3 arbitrage.py -m MtGoxEUR get-balance
    $ python3 arbitrage.py -m MtGoxEUR,MtGoxUSD,BitstampUSD get-balance

Run tests

    $ nosetests arbitrage/

# DESIGN

The Arbiter class is responsible for finding all trade opportunities and informing registered observers about them.
It does this at configured intervals, by downloading the current market orders, tickers, and running the 'arbitrage_simple_opportunity' algorithm.

'arbitrage_simple_opportunity' only compares the lowest 'ask' price and the highest 'bid' price.
There's also 'arbitrage_depth_opportunity' algorithm which is capable of taking several bids and asks into account, but it's not used right now.

Once an arbitrage opportunity is identified, it is sent to an Observer with several parameters like: profit percentage, potential volume, etc.
The Observer is also notified of beginning and end of each opportunity search round.
This way the Observer has a selection of opportunities to choose from.

There are multiple implementations of Observer - Logger, TraderBot, EMailer, etc.
The bots used for automated trading are:
 * TraderBot - a BTC/USD bot which memorizes all arbitrage opportunities from one round, sorts them by profitability and executes the best one, while:
  * the profit must be above a configured profit percentage threshold,
  * both exchanges must be configured and automated,
  * the traded amount can never be lower than config.min_tx_volume,
  * the traded amount will be capped by config.max_tx_volume,
  * the bot will leave at least config.balance_margin  percent of current balance untouched.
 * SpecializedTraderBot - a version of TraderBot designed for EURO exchanges, also includes some fixes for corrupted data sent by some exchanges,
 * TraderBotAltCoin - a TraderBot for BTC/AltCoin trades.

All markets have a public and private interface.
The public one provides orders and tickers, and is used for opportunity search.
The private one provides access to your account on that exchange and allows for automated trading by bots.

All access keys, and settings are stored in the config.py file.

# TODO

 * Tests
 * Write documentation
 * Add other exchanges:
   * icbit
   * BitFinex
 * Update order books with a WebSocket client for supported exchanges
   (MtGox, Bitcoin-Central)
 * Better history handling for observer "HistoryDumper" (Redis ?)
 * Move EUR / USD from a market to an other:
   * Coupons
   * Ripple ?
   * Negative Operations
   * use Litecoin or other cryptocurrencies trades

# LICENSE

MIT

Copyright (c) 2013 Maxime Biais <firstname.lastname@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
