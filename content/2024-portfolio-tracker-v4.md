+++
title = "Portfolio Tracker v4"
date  = 2024-05-09
description = "Revamped my Portfolio Tracker with multi-wallet support and improved allocation."

[taxonomies]
tags = ["finance"]
+++

Last year I published my portfolio tracker spreadsheet along with [a blog post on how I built it](@/2023-portfolio-tracker.md).  Over time, I noticed that I mixed some concepts in that spreadsheet as I transitioned my strategy from stock picking to core-satellite.  So, I decided to revamp the spreadsheet to align it with the new strategy, dropping unnecessary features and adding new ones.  In this post, I'll share how the new portfolio tracker works and how I built it.

**tl;dr:** Link to the spreadsheet [here](https://docs.google.com/spreadsheets/d/1t74zQb31tufTZNpCdS2CkfEboHgqzjcXPSthOC8HA_M/edit?usp=sharing).  Donation [here](https://www.paypal.com/donate/?hosted_button_id=2W2ALYSGHTDTU).


## Requisites
In addition to the requisites described in the previous post, this spreadsheet should be capable of managing multiple wallets.  With it, one can manage multiple funds (up to five) like "Retirement" and "House Purchase".  Other requisites, such as performance and acceptance of multiple currencies, are here, and most of the previous formulas and ideas were reused.

To avoid past mistakes, I revamped the asset taxonomy so that each asset now has these properties:

1. **Wallet:** The wallet where the asset is allocated, allowing the user to have one asset in multiple wallets.
2. **Type:** The asset type, Core or Satellite.
3. **Class:** The investment type, fixed or variable income, or both.
4. **Currency:** The currency in which the asset is traded.
5. **Asset:** Asset's ticker.

The core structure of the spreadsheet was maintained, with four tabs:

1. **Setup:** Where the user sets up basics like wallets, assets, and starting year.  This spreadsheet is designed to track assets over a five-year period, see the next note.
2. **Ledger:** Aggregates transactions like buy, sell, and incomes from assets.
3. **Allocation:** Used to define the asset allocation strategy in all layers (Wallet, Type, and Assets).
4. **Summary:** Summarizes data from other tabs, especially from Allocation, showing the user insightful information.

{% admonition(type="note", title="Note") %}
As this spreadsheet is limited to tracking assets over five years, the user is invited to use multiple files for tracking assets over larger periods of time.  My recommendation is to keep these files in the same folder with the date range in the file name, like `Portfolio Tracker 2019-2023`.  When starting a new file, you can drop deprecated assets and summarize transactions to make it even faster.  I do this using separate code that summarizes transactions in the Ledger tab, then I copy-and-paste the values into the new sheet.  As a reference, I use the date of December 31st of the previous year in the summarized transactions.
{% end %}


## Allocation
I've shifted the focus from asset taxonomy to asset allocation here, making it more user-friendly and visually guiding the user to define their targets properly, what's more important.  The first step is to define the allocation between wallets and how each wallet will be divided between core and satellite.  With this setup, all assets must be cataloged with their respective weights.

The weights make sense locally (Wallet/Type), considering both the target allocation from the wallet and the type (core-satellite).  I chose to use weights here to make it easier for me to define target allocations for assets.  With the weights (which may vary between 0 and 20), the spreadsheet will calculate their percentages in the same Wallet/Type and then figure out how it reflects in the previously defined targets.

> **Example:** If there's only one asset in the Retirement wallet and its type is Core, and that type is set to 70%, no matter the weight given, it'll be 70% of that wallet.  In the same scenario, if there are two assets with the same weight, each one will receive 35% of that type inside that wallet, resulting in 70%.  *Trust me, it's easier to do than to explain.* 😅

{% admonition(type="warning", title="Important") %}
As the core-satellite strategy uses ETFs and each ETF can diversify between assets, usually there are just a few assets in portfolios like this.  So I limited the spreadsheet to "only" 50 assets.  It's more than enough for an investor who knows what she's doing, even considering a time span of five years.  If you need more space, I think you should review your strategy.
{% end %}


## Implementation
It was a fast track for me since this is the fourth time I've rewritten this spreadsheet, but here I want to highlight some important formulas I implemented along with important project decisions.  I found a bug in the formula to get the last transaction of an asset in the Ledger tab when the first and second assets (rows 4 and 5) are the same.  That happens because they end up calling `ArrayFormula()` with just one row.  I fixed it manually by evaluating row 5 using an `If()`:

```swift
=if(
  isblank(H4),,
  if(
    row()<>4,
    if(
      row()>5,
      max(arrayformula(if(
        (indirect("B4:B"&row()-1)=B4)*(indirect("C4:C"&row()-1)=C4),
        row(A$4:A))
      )),
      if(and(B4=B3,C4=C3),4,0)
    ),
    0
  )
)
```

As previously mentioned, the weighted targets were a good challenge as I had to calculate the weight percentage locally (Wallet/Type) and then find the target allocation for it to get the target allocation for that Asset inside the Wallet and Type.  The formula ended up like this:

```swift
=if(
  isblank(K16),,
  iferror(F16/sumifs(F$16:F,A$16:A,"="&A16,B$16:B,"="&B16),0)*filter(G$3:G$12,A$3:A$12=A16,F$3:F$12=B16)
)
```

One of the biggest changes in this version was the addition of automatic portfolio evolution by year.  I've limited this to a five-year range to maintain performance and added new hidden columns as pivots to the end of the Allocation tab.  Thanks to the limits, like the number of Assets and Wallets, and the time span, even though these formulas are complex, I've noticed no downgrade in overall performance.

### Crypto and Deprecated Assets
As [I showed in another post](@/2022-gsheets-crypto-prices.md), Google Finance is able to fetch quotes for some major cryptocurrencies, but for most it just can't.  It's possible to implement it using another source, but as I'm avoiding third-party scripts here, there's no easy way to implement fallbacks like *"if `GoogleFinance()` yields an error, try to get the quote here"*.  This way, I leave it to the user's discretion to implement it or not if she's investing in such assets.

The same concern applies to deprecated assets.  As Google Finance terminates the asset/ticker when a security is no longer being traded, it'll yield an error.  For one specific case, to get the normalized asset value on December 31st of a given year, if this function returns an error, the spreadsheet will consider the average price of that asset instead.  The formula goes like this:

```swift
=if(
  isblank(K16),,
  if(
    AA16<=0,0,
    if(
      D16=DefaultCurrency,
      AA16*ifna(index(googlefinance(E16,"price","12/31/"&AA$14),2,2),AB16),
      AA16*ifna(index(googlefinance(E16,"price","12/31/"&AA$14),2,2),AB16)*index(googlefinance(D16&DefaultCurrency,"price","12/31/"&AA$14),2,2)
    )
  )
)
```


## Download, Donation, and Last Words
You can **get the spreadsheet** [here](https://docs.google.com/spreadsheets/d/1t74zQb31tufTZNpCdS2CkfEboHgqzjcXPSthOC8HA_M/edit?usp=sharing).  It'll require a Google account with Google Drive access.  Make a copy of the document to your account and start working.  I recommend starting with the Setup tab and moving left: Add your transactions and incomes in Ledger (if any), define your asset allocation in the Allocation tab, and track your evolution in the Summary tab.  Again, **if this spreadsheet helped you in any way and you're able and comfortable to donate any value**, you can do it [in this link](https://www.paypal.com/donate/?hosted_button_id=2W2ALYSGHTDTU).  Minor changes, like little improvements and bug fixes, will happen silently and the document will reflect them, as usual.

This new version fixes some conceptual problems from the last one, tightens up the limits for assets, and implements long-desired features.  The Summary is filled only with relevant information (learned from previous versions) and allows the user to automatically track her portfolio evolution.  The Allocation is more friendly and visually guides the user on the portfolio distribution: Wallet allocation, Type (Core-Satellite) allocation, Asset allocation.  The ledger is almost the same, but slightly simpler and as things are less complex here, the setup offers fewer options.

Happy investing!
