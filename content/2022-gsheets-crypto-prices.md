+++
title = "Getting Cryptocurrency Prices in Google Sheets"
date  = 2022-12-26
description = "Learn how to get minor cryptocurrency prices in Google Sheets using Crypto Prices."

[taxonomies]
tags = ["finance"]
+++

Google Sheets is far better than MS-Excel (IMO) and its `googlefinance()` function is among one of most used features in my financial spreadsheets.  Although very powerful, allowing you to get quotes in a streamlined way, it lacks support for many cryptocurrencies  --and I'm not talking about obscure, unknown coins.


## Standard Way
The standard way of getting quotes, specially for Stocks, works for the most popular cryptocurrencies, like Bitcoin, Ethereum, and Cardano.  Just use the `googlefinance()` function and pass the cryptocurrency code as parameter concatenated with the currency code (fiat money), like USD or BRL.  Examples:

```
=googlefinance("BTCBRL")  # gets the Bitcoin (BTC) price in Brazilian Real (BRL)
=googlefinance("ETHUSD")  # gets the Ethereum (ETH) price in US Dollar (USD)
```

Despite being very easy and trustworthy as stated before, this function does not support minor coins, like [Polygon](https://en.wikipedia.org/wiki/Polygon_(blockchain)) and [Chainlink](https://en.wikipedia.org/wiki/Chainlink_(blockchain)).


## Workaround
The common workaround for this type of situation is to find a website that brings the updated coin prices and use the `importxml()` function in Google Sheets to extract the data based on its [XPath](https://en.wikipedia.org/wiki/XPath).  Although it's a good solution, nowadays many websites use JavaScript to render the page and that completely breaks this approach, because the JavaScript code tends to be fetched instead of the price itself.

Other services allow us to get prices programmatically through APIs but they require authentication and it would require writing functions in Google Sheets, what would require a more complex approach to solve the problem.  Fortunately, the [Crypto Prices](https://cryptoprices.cc/) site provides an easy way to get Cryptocurrency prices by particular URLs.  This service is like an abstraction for [CoinGecko API](https://www.coingecko.com/en/api), that uses the coin tickers instead the coin names to get data (which makes more sense to me), but limits to price data --which is enough for this goal.

Crypto Prices’ homepage itself shows usage examples for Google Sheets with the help of `importdata()` function: Just append coin code to the base URL for Crypto Prices and pass it to `importdata()`:

```
=importdata("https://cryptoprices.cc/MATIC/")
=importdata("https://cryptoprices.cc/LINK/")
```
And that's all: The cryptocurrency price in US Dollar will be fetched and can be used in the spreadsheet.  But what if you're using a different location in the spreadsheet (different [decimal separator](https://en.wikipedia.org/wiki/Decimal_separator)) or simply not using US Dollar?

### Location
This method works well with `en_US` locations, but if you're using another location with ~~a better~~ another number format, it'll bring incorrect values although the function won't break --like bringing `801457` instead of `0.801457`.  To fix it, make sure to pass the correct location to `importdata()` in the third parameter, like this:

```
=importdata("https://cryptoprices.cc/MATIC/";;"en_US")
=importdata("https://cryptoprices.cc/SOL/";;"en_US")
```

In this case, the locale will always be `en_US` because we can't change that in the source (as a parameter in the URL, for instance).  Furthermore, the result will always come in US Dollar (USD), so if you want to change it, just multiply the output by the price of your desired currency in comparison to USD.  The `googlefinance()` function can do that conversion easily just by concatenating the [currency codes](https://en.wikipedia.org/wiki/ISO_4217), like `USDBRL` (US Dollar to Brazilian Real) or `JPYEUR` (Japanese Yen to Euro).  So, to get the Chainlink price in Brazilian Real in a spreadsheet with Brazilian locale, the formula would be:

```
=importdata("https://cryptoprices.cc/LINK/";;"en_US") * googlefinance("USDBRL")
```

> Translation: Get the Chainlink price in US Dollar from Crypto Prices (remember it'll come in en_US locale), and multiply it by the Brazilian Real value in front of US Dollar, so I'll get the Chainlink quotation in BRL in my Brazilian spreadsheet.

That's it!
