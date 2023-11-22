+++
title = "Portfolio Tracker: Mastering your Finances"
date  = 2023-08-23
description = "Enhance your portfolio management with this free, powerful spreadsheet —empowering investors to make informed decisions."

[taxonomies]
tags = ["finance"]

[extra]
math = true
+++


Regrettably, from my experience with software, I discovered that there was no effective solution to assist me in managing my portfolio.  This was due to either their high cost or their lack of utility.  I'm not interested in applications that merely display obvious information, such as the number of shares I hold for an asset.  I require more than that; I desire actionable information that can genuinely enhance my profits.

The most effective solution I encountered was creating my own spreadsheet, and here, I will outline the process I undertook and share my personal **Portfolio Tracker** spreadsheet.

**tl;dr:** [Download link](https://docs.google.com/spreadsheets/d/1LH5jnCaQoDd7uHoTXZCTHdQk2d0va4k_uAmjmojcZbo/edit?usp=sharing) and [Donation link](https://www.paypal.com/donate/?hosted_button_id=2W2ALYSGHTDTU).


## Requirements

I initiated the process by clearly defining a list of requirements to guide the development:

- The spreadsheet must exhibit speed, as waiting 5-10 seconds to update quotes and overall values is undesirable.
- Simplicity is a key principle to eliminate extraneous data and improve performance.
- Exclusively built-in formulas should be used within the spreadsheet to mitigate security concerns and optimize performance.
- It should support multiple currencies, enabling worldwide investment tracking.
- Asset classification should be possible based on factors such as geographic location and sector grouping.

Being an investor, I understand the significance of tracking assets, both for personal understanding and for tax purposes.

- It should facilitate setting up targets for asset allocation, considering criteria like geographic location.
- Current asset allocation needs to be monitored to enable users to compare against targets for further allocation decisions.
- Text-based dashboards presenting pertinent information should be provided.  While charts are appealing, they tend to load slowly and compromise performance.
- A single point for recording transactions and incomes is necessary.
- A unified point to record management data, including geographic location, classes, sectors, and tickers, is essential for users to customize the spreadsheet.
- Regarding colors, the spreadsheet must adhere to a specific palette, allowing users to modify only cells without background color.


## Structure and Basics

With the requirements set, after several tests, I established the following structure for the spreadsheet:

- **Summary:** Offers a high-level overview, aggregating all dashboards.
- **Portfolio:** Used to define asset allocation and consolidate transaction data.
- **Ledger:** The heart of the spreadsheet, where all transactions and incomes are recorded.
- **Setup:** Encompasses all user settings, affecting the entire spreadsheet.

Despite my preference for the metric system, I decided to use the US locale for this spreadsheet.

In terms of colors, I opted for a **colorless background to denote cells where users can input data**.  Dark gray serves as the default text color, and light gray is employed in cells with formulas.  Green and red represent gains and losses, respectively, in text colors for certain data types.

Personally, I find monospaced fonts highly readable at smaller sizes.  Therefore, I selected [Consolas](https://www.dafontfree.io/consolas-font/) as the default font with a font size of 8.  Consequently, the default column width was adjusted from 100 to 90.

It's crucial to mention that I chose [Google Sheets](https://www.google.com/sheets/about/) as the underlying tool.  While many prefer Excel, I'm more accustomed to Sheets, and in my opinion, it surpasses its renowned counterpart.


## Setup

I began constructing the Setup Sheet since it doesn't rely on other Sheets and is a simpler sheet without formulas.  Here, I define supported currencies, actions, income types, as well as the taxonomy for classifying various assets.

### Currencies

This entails a list of currencies that need to be declared alongside their respective [ISO 4217](https://docs.1010data.com/1010dataReferenceManual/DataTypesAndFormats/currencyUnitCodes.html) codes.  The first code assumes a special role, becoming the **default currency** throughout the entire spreadsheet.

### Actions

These are the supported trading actions within the sheet.  As of this writing, they include:

- Buy
- Sell
- Split
- Reverse split
- Dividend Stocks

All options are self-explanatory, except for **Dividend Stocks**, which warrants clarification.  It serves as both an action and an income type.  When an asset pays dividends in the form of stocks, users must record this as a trade to update their position and as an income to document the generated cash inflow.

### Income Types

This section outlines the ways assets can yield returns to investors:

- Dividend
- Dividend Stocks
- BR/JCP
- BR/Rendimento
- BR/Restituição

While Dividend is well-known and Dividend Stocks was previously explained, the triad `BR/(JCP|Rendimento|Restituição)` is specific to Brazilian investors, representing local income types.

### Asset Taxonomy

Creating an asset taxonomy that fits most cases is challenging, but I've made an earnest attempt.  The taxonomy employed in this spreadsheet consists of five layers:

- **Geoclass:** Denotes the geographical location associated with assets.
- **Class:** Represents the asset's macroclass.
- **Equity, Debt, and Fund Classes:** Signifies the asset subclass within the Class.
- **Equity, Debt, and Fund Dimension:** Referred to as “dimension,” this evaluates asset subclasses, heavily influenced by MorningStar matrices.
- **Equity, Debt, and Fund Sectors:** Specifies the sector the asset belongs to.

### Brokers

This section lists the brokers where users conduct trades.  Currently, this is optional, but it might contribute to future dashboard compositions.

### Assets

Codes used to symbolize assets.  Formulas in other Sheets utilize these symbols to fetch the most recent asset quotes, determining the portfolio's market value.


## Ledger

This Sheet encompasses three tables:

- **Transactions:** Records all transaction data.
- **Incomes:** Documents income generated by assets.
- **Pivots:** Aggregates pivot tables from the Transactions and Incomes tables.

> **Important:** In this Sheet, I've abstracted the concept of currency and focused solely on values.  Currency becomes relevant when transaction values are interpreted in the Portfolio and Summary layers.

### Transactions

This table consolidates all information regarding asset transactions.  Users need to provide:

- **Date:** The transaction date.
- **Broker:** Optional.  The broker used for the transaction.
- **Asset:** The symbol of the traded asset.
- **Action:** The action performed.
- **Shares/Ratio:** The quantity of shares or Split/Rev. Split ratio.
- **Price:** The asset's price.
- **Fees:** Optional.  The cost of the operation.

While Broker and Fees are **optional**, they contain valuable user information.  For Split and Rev.  Split actions, price and fees are disregarded.

> **Important:** It's assumed that all financial values (price and fees) share the same currency within each transaction.

This table yields the following values:

- **Amount:** Total transaction cost.
- **Position:** Number of shares owned after the transaction.
- **Average Price:** Updated average asset price.
- **Cost:** Total acquisition cost per asset.
- **Realized Gain:** Profit or loss post-sales.
- **Last Transaction:** A column tracing the previous transaction (row) for that asset.  This column is hidden.

#### Average Price

The average price is updated with every operation except sales.  Its formula is as follows:

$$
  avgprice = \frac{amount + cost}{position}
$$

For Split and Rev. Split actions, the Average Price is divided or multiplied by the Ratio.

### Incomes

This table records all types of income received from assets:

- **Date:** Date of income receipt.
- **Asset:** Symbol of the paying asset.
- **Type:** Income type.
- **Amount:** Value received.
- **Taxes:** Optional.  Any taxes deducted.
- **Total:** Income value minus taxes paid.

Taxes can be made optional if not being tracked.  Just ensure to indicate the income value post-tax deduction.

### Pivots

The Pivots table aggregates summaries from both the Transactions and Incomes tables.  Other Sheets consume this table, especially the Portfolio Sheet, to retrieve the current status of investments.  This table is hidden from the user because it's needed only for inner controls.


## Portfolio

The Portfolio Sheet consolidates both allocation and portfolio management capabilities.  Here, investors can establish target allocations as percentages based on Geoclasses and Geoclass / Asset Classes.  After setting these targets, they can begin populating the portfolio with assets, categorizing them, and assigning weights that the spreadsheet will then convert into percentages according to their Geoclasses and Classes.

{% admonition(type="note", title="Note") %}
While the taxonomy includes five layers for asset classification, only three are utilized to set asset allocation: Geoclass, Class, and the asset itself.  The other layers provide guidance for allocation targets but are purely informative.  Consequently, I define my allocation based on these three layers, then observe the resulting allocation based on the remaining three layers (Subclass, Dimension, and Sector), potentially making adjustments—such as increasing or decreasing investments in a sector, adding or removing a dimension, or incorporating more assets of a subclass.
{% end %}

I opted for using percentages for higher categories (Geo and Class) and weights for assets, as defining percentages for a few items is simpler, whereas it becomes more complex for a larger set.  Should I choose to use percentages for assets, I'd need to alter nearly all percentages for assets in the same Geo/Class.  Additionally, by employing weights, I can establish personalized rules based on Geo/Class.  For instance, I might use a scale of 0-10 for Stocks in Brazil and 0-5 for ETFs in the US.

{% admonition(type="note", title="Note") %}
Assigning a weight of zero (0) is applicable to assets in the portfolio that are no longer desired but haven't been sold yet.  For such assets, the spreadsheet will automatically mark them for sale, as their target allocation has become zero.
{% end %}

Lastly, this Sheet starts to **contextualize monetary values in terms of currencies**.  In the Ledger, values were currency-agnostic, but here, these values are linked to currencies.  The ultimate output normalizes everything in terms of the default currency (established on the Setup Sheet), achieving standardization.  It's important to note that while assets themselves make sense in their respective currencies, when we analyze assets collectively within a portfolio, we need the concept of default currency to compare entities on an equal basis.  Otherwise, we would be juxtaposing values in US Dollars against values in Brazilian Reais, which lacks coherence.

Now, onto the technical details.  This spreadsheet comprises five tables:

- Portfolio Overview
- Geoclass Allocation
- Class Allocation
- Subclasses Allocation
- Asset Allocation

### Portfolio Overview

This table aggregates key data from the portfolio:

- **Cash:** Available funds for investment, taken into account in allocation formulas.
- **Total Cost:** The sum of money used to purchase assets: 

$$
  \sum position \cdot avgprice
$$

- **Market Value:** The updated portfolio value considering the most recent price of each asset:

$$
  \sum position \cdot lastprice
$$

- **Unrealized Gain:** The difference between Market Value and Total Cost.
- **Realized Gain:** The cumulative profit or loss from all sales.

### Geoclass Allocation

Here, you can set target allocations (in percentages) for each geographical location where you're investing, and monitor the current allocation.  This allows you to discern where to direct your efforts.

### Class Allocation

Similar to Geoclass, but focused on asset Classes.  Note that unlike Geoclass, each Class is linked to a Geoclass.  Consequently, you need to fill in all Classes for each Geoclass.

> **Pro tip:** Suppose you allocate 60% overall to the US.  Within the US, you might want to split those 60% into, for example, 90% for Stocks and 10% for REITs.  So, your allocation reads: Out of those 60%, I'll allocate 90% here and 10% there.

### Subclasses Allocation

This section provides supplementary data that proves valuable for fine-tuning allocation.  After defining all allocations, including Asset allocation (covered in the next section), this table aggregates target percentages based on Sectors, Domains, Subclasses, and Currencies.  With this information, you can adjust values to more accurately represent your portfolio intentions.

### Asset Allocation

This table takes center stage, as it's where you record each asset to construct your portfolio.  Properly classifying assets using Geoclass, Class, Subclass, Domain, Sector, and Currency, you can then assign a weight to each asset.  This is where the magic unfolds, as the spreadsheet undertakes the complex task of calculating your current situation, the desired scenario in terms of currency, and the necessary actions to achieve your objectives.  Here are the columns this table generates for your analysis:

- **% Target Geo/Class:** The target percentage of the asset compared to others within the same Geoclass and Class (localized analysis).  This is calculated using the previously provided weight.
- **Action:** This field employs the Target Allocation (optimal allocation for each asset) and Market Value (current allocation of each asset) to determine the recommended action for each asset—whether to buy more shares, sell shares, or maintain the position.  This action is rooted in the Buy & Hold approach.
- **Action $:** An extension of the previous value, indicating the amount of money required to attain the optimal scenario.
- **Participation:** The current percentage of allocation for each asset across the entire portfolio (not confined to Geoclass and Class).
- **% Current Geo/Class:** Essentially the same as the above, but using current data (Last Price).
- **Target Allocation:** Based on the portfolio's market value, available cash for investment, the percentage of that Geoclass, the percentage of that Class, and the % Target Geo/Class, this figure calculates the optimal monetary allocation for the asset.
- **Position:** The quantity of shares held for each asset.
- **Average Price:** The average price at which shares were acquired.
- **Last Price:** The most recent traded price of each asset.
- **Cost:** The sum of money expended to acquire the current position:

$$
  position \cdot avgprice
$$

- **Market Value:** Almost identical to Cost, but using Last Price instead of Average Price.
- **Unrealized Gain:** The difference between Market Value and Cost—representing the gain/loss of unsold shares.
- **Realized Gain:** Similar to Unrealized Gain, but limited to sales.
- **Incomes:** The earnings accrued from each asset.
- **Yield on Cost:** Incomes divided by Cost.  When this reaches 100%, it indicates that you've reached the break-even point for that asset.
- **Normalized \*:** All other columns factor in monetary values linked to each asset's currency.  However, normalized columns (the rightmost ones) convert everything to the Default Currency (defined in the Setup Sheet).  This is crucial because from this point until the next Sheet (Summary), all data must be standardized to avoid comparing assets in different currencies, which would be impractical.

#### Last Price

This spreadsheet employs the `=GoogleFinance()` formula to fetch the most recent quote for each asset.  As indicated by Google, this value has a delay of approximately 15 minutes.  Unfortunately, Google Finance doesn't encompass all quotes, especially for Cryptocurrencies.  Thus, for some assets, users must replace the Google Finance formula with alternative methods to retrieve this data.  In such cases, be aware that you can use the spreadsheet's autofill feature to regain the original formula.


## Summary

As the name implies, the Summary Sheet consolidates all data from the Portfolio to offer a comprehensive overview of investments.  In this spreadsheet, all currency-related data is converted into the Default Currency to ensure accurate comparisons.  While numerous data visualizations could be generated with the information from other sheets, a primary principle of this project is simplicity for enhanced performance.  Bearing this in mind, I constructed three tables here:

- **Top 10:** Enlists the top 10 gains, losses, contributors, and Yield on Cost.
- **Geoclass Analysis:** Provides an overview of Geoclass allocations, encompassing:
  - Cost
  - Market Value
  - Unrealized Gain
  - Current Allocation (%)
  - Target Allocation (%)
  - Incomes
  - Average Yield on Cost
  - Allocations by Classes, Subclasses, Dimensions, and Sectors.
- **Portfolio History:** Chronicles the annual evolution of the portfolio.  This history proves crucial for assessing whether you're on the right path.
- **Subcategory Analysis:** Tracks the Unrealized Gain per subcategory regardless the Geoclass.  The Subclass is avoided here because it is complementary to Class (which is present) and the lack of space solely.

I refrained from generating charts with this data, as they tend to impact performance negatively.  Instead, I found it straightforward to analyze the data through dashboards.  Each piece of data presented here underwent a cost-benefit analysis to deliver the most valuable insights into portfolio health while minimizing any adverse impact on performance.


## Usage

With all components in place, it was time to put the spreadsheet to use.  Here's my approach to utilizing it effectively.

### Initial Use

My introduction to the spreadsheet involved a reverse method: I began by opening the Setup Sheet and personalizing it according to my needs.  This entailed configuring the currencies I invest in, adding my brokers, and listing the assets I currently own or plan to acquire.

Subsequently, I transformed my prior tracking system into a format compatible with the Ledger.  This required transferring historical trade data and income earnings to the Ledger while making manual adjustments as needed.  Although somewhat tedious, this step was essential to ensure the accuracy of asset allocation and avoid any inconsistencies.

Transitioning to the Portfolio Sheet, I established target allocations for Geoclasses and each Class within those Geoclasses.  I meticulously added assets to the Asset Allocation table, assigning them appropriate classifications and weights based on how much I wanted them to contribute to my portfolio.

Lastly, I evaluated the set targets and made necessary adjustments to both percentages (Geoclass and Class allocations) and asset weights.  After confirming that the current values aligned with my goals and there were no inaccuracies from the Ledger, I moved on to the Summary Sheet.  Here, I reviewed the data displayed and manually entered historical portfolio information into the Portfolio History section to cover the past five years.

The initial setup process demands more effort and time due to the various actions required.  However, careful execution at this stage leads to time savings in the future and a heightened level of control over assets.

### Subsequent Use

I typically use this spreadsheet on a biweekly or monthly basis, depending on circumstances.

When I'm infusing additional funds into my investments, I input the available amount into the Cash section within the `Portfolio/Portfolio Overview` table.  Then, I consult the Actions column to see the spreadsheet's recommendations.  During this analysis, I consider both the target amount for each asset and the "Unrealized Gain %" of each asset.  My investment prioritization follows a sequence: (a) assets marked with Buy, (b) among them, assets with the lowest positions, (c) assets with lower appreciation, and (d) assets exhibiting a higher tendency for appreciation (based on my analysis not tracked by the spreadsheet).  Once I've decided on my course of action, I proceed to execute the necessary transactions via my broker, after which I update the Ledger to maintain up-to-date records.

Beyond trading activities, I occasionally open the spreadsheet to assess the portfolio's progress or to record income received during the period.  For portfolio analysis, I rely mainly on the Summary and Portfolio Sheets.  When updating income, I utilize the relevant income table within the Ledger.

Unlike the initial usage, regular usage tends to be straightforward and efficient, unless I've altered my investment strategy and need to reflect those changes in the spreadsheet—such as phasing out certain assets or recalibrating target allocations.

{% admonition(type="note", title="Note") %}
It's important to recognize that regularly adjusting allocations is natural and beneficial.  However, excessive recalibration can impact results, complicate portfolio tracking, and potentially reduce profits.  Personally, I find recalibrating once a year sufficient, though the intricacies of an investment strategy extend beyond the scope of this spreadsheet.
{% end %}


## Donations and Download

If you find this spreadsheet beneficial and feel inclined to contribute, consider making a **donation** of any amount.  Rest assured, I will utilize each donated penny judiciously.  :)

You can find the donation button in the bottom-right corner of the Summary Sheet, or you can use [THIS LINK](https://www.paypal.com/donate/?hosted_button_id=2W2ALYSGHTDTU).  Your recognition and support are greatly appreciated!

**Download** the spreadsheet [HERE](https://docs.google.com/spreadsheets/d/1LH5jnCaQoDd7uHoTXZCTHdQk2d0va4k_uAmjmojcZbo/edit?usp=sharing).  When the link opens, click on `File > Make a copy` and then you're good to go!

I wish you all success!  Avante!
