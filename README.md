# Explaining The 2019 UK General Election


## The Objective

Analytically identify the drivers for the result of the 2019 UK General Election.

## Table of contents

   * [The Motivation](#the-motivation)
   * [The Technologies Used](#the-technologies-used)
   * [The Process Overview](#the-process-overview)
   * [Data Gathering](#data-gathering)
       * [Intro to UK Politics](#intro-to-uk-politics)
       * [Data Sources](#data-sources)
       * [EDA](#eda)
   * [Modelling](#modelling)
       * [Algorithms Used](#algorithms-used)
       * [Permutation Importance](#permutation-importance)
   * [Conclusion](#conclusion)


## The Motivation

On September 2nd, Boris Johnson, the UK’s Prime Minister, stood on the steps of 10 Downing Street and announced that he didn’t want a general election. Anyone with an ounce of political nous knew what was coming next.

On the morning of Friday the 13th (naturally) of December, the country awoke to find that it had given Boris Johnson the biggest Conservative election win since the days of Margaret Thatcher.

![BorisJohnson](https://github.com/calbal91/project-understanding-elections/blob/master/Images/boris.jpg)

Many places voted for the Conservatives for the first time, with the opposition Labour party suffering its worst defeat since before WW2. Could this result have been predicted? And what were the greatest factors that caused such a result?

Understanding these things could help Labour avoid such a grand-scale defeat in the future (or help the Conservatives maintain a compelling majority when the next election comes).


## The Technologies Used

* Pandas for data munging
* XGBoost, and Scikit-learn Random Forest / Decision Tree classifiers for building classification engines
* Scikit-learn GridSearchCV for hyperparameter tuning
* eli5 for Permutation Importance
* Matplotlib and Plotly for data visualisation


## The Process Overview

1. If we expect a model to tell us which factors were the most important in the election, we need to find some factors for it to choose from. In particular, we should scrape / download as much constituency-level data as possible from official government sources as we can.

2. Since this analysis was conducted in the week running up to the general election, we couldn't use the actual results as our target variable. Instead, we used the results of YouGov’s MRP Poll published on November 27th (just over two weeks before polling day). MRP (Multilevel Regression with Poststratification) is an advanced polling methodology, and the YouGov poll in question had a huge sample size, with over 100,000 people responding in the space of seven days. It was therefore able to give a projected vote share for each party in every constituency. In the event, it slightly underestimated the Conservative success in seat terms, though its projection of overall vote share was within 1–2% of the final result for the main parties.

3. Having built a dataset, we train classifier models. We then take the most accurate models, and perform permutation importance on them to see which features were most decisive. We can infer that these were the most critical factors in the election result.


## Data Gathering

The data gathering process for this project was significant. The dataset had over 100 features for each constituency in England and Wales, with most features covering all UK constituencies.

### Intro to UK Politics

Political power in the UK lies in the House of Commons (or Parliament). This is populated by 650 members (MPs), one from each of the UK’s 650 ‘constituencies’.

In a general election, each constituency votes for a single MP from a selection of candidates. The one candidate with the most votes in that constituency is elected as an MP. The political party that has the most elected MPs overall wins the election, and typically forms the government. This system is known as ‘First Past The Post’ (FPTP).

##### The 2019 results - each hex represents one constituency

![2019Results](https://github.com/calbal91/project-understanding-elections/blob/master/Images/2019map.png)

This is a voting system that unambiguously helps the UK’s two main parties — the Conservatives and Labour, who have won every general election since 1922.

Another phenomena that arises from FPTP is ‘safe seats’ — constituencies that are almost certain to vote a given way. In the run up to the 2019 election, there were 170 constituencies that have voted for the same party since World War 2 (the constituency of North Shropshire is probably the safest seat of all — it has been held by the Conservatives since 1834).

This idea of 'safe seats', and seats that are closely faught ('marginals' in the lingo) will be important later.

##### The main parties, represented on the political compass, as at the 2019 election

![PoliticalCompass](https://github.com/calbal91/project-understanding-elections/blob/master/Images/compass.png)

### Data Sources

There exists no single 'master' database of different constituency statistics that is publically available. Happily, a lot of official data is available at a constituency level through government websites, in particular https://commonslibrary.parliament.uk/local-data/

We can pull various CSVs together to create a grand table of constituency-by-constituency KPIs across a range of metrics; from house prices, weekly wages, and population density, to educational attainment, ethnicity, and even broadband quality.

##### The list of KPIs used as features in this project

![KPIs](https://github.com/calbal91/project-understanding-elections/blob/master/Images/kpis.png)

### EDA


## Modelling

### Algorithms Used

### Permutation Importance


## Conclusion

This has significant implications for investors - we can see how different the fortunes of two investors could have been if they'd invested in, respectively, the best and worst zip codes in the 20 years from 1996.

![Investments](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/BestWorstInvestment.jpg)

If we look at the data at a state by state level, we can see how property prices have grown (and fallen) in different areas of the USA:

#### Prices as at April 2018

![PriceByState](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/StatePrices.png)

#### Price growth during financial crisis

![PriceTrend](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/StatePriceGrowth07.png)

#### Price growth since financial crisis

![PriceTrend](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/StatePriceGrowth12.png)


One thing to note here is that whilst some states have grown very strongly in the last few years, many of these states were the ones worst hit by the crash - it may in simply be the case that they've been recovering their value, and don't offer good long-term potential.

![StateGrowth](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/StateGrowth.jpg)

This is all very important context for when we come to fit our forecasting models:

* We should not use the full historical data to train our models. The financial crisis had a material effect on the shape of the time series, but was (we can only hope) a freak exogenous event. If we don’t expect something similar to happen again, then we shouldn’t let our model train itself on that data from that period. We should therefore focus on price data since April 2012…
* However, taking this approach brings its own problems. By using only the last six years of data, a model might assume that regions in states like Nevada and Florida are sure bets, and enthusiastically recommend that we invest there. Of course, we know that growth in these cases was simply recovery from a rough recession. In any case, we should be wary of investing in regions that have the potential to shed half their value in the event of an unforeseen economic shock.


## Selecting Regions

We can get around this by discounting many of our zip codes out of hand straight away (given that we only need to choose 5 anyway).

We plot all the regions on a scatter, with recent price growth on the horizontal and price growth during the financial crisis on the vertical.

![ZipScatter](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/ZipScatter.png)

By looking at regions that are above average in both measures, we can isolate places that both weathered the storm between 2007 and 2012 (demonstrating at least some resilience against economic shocks) and also strong growth potential for future years.

Therefore, these 1,737 ‘Growth Regions’ are all areas that we can, in principle, feel comfortable recommending to investors. Indeed, by indexing April 2007 prices in these regions, we see that they have outperformed other regions since at least 1996.

![GrowthRegions](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/GrowthRegionGrowth.png)


## Modelling Forecasts

Having checked that our data is neither stationary nor seasonal, we can go about producing individual ARIMA models for each zip code.

#### Further detail on ARIMA modelling...

![ARIMA](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/ARIMA.jpg)

Having found an optimal ARIMA model for each zip code, we can see that some regions are forecast to experience very strong growth. However, some forecasts come with a very wide range – and would therefore make for risky investments.

Again, we can focus on the best zip codes - those with strong growth potential, but also with narrow confidence intervals (and thus less intrinsic risk).

![ZipRiskScatter](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/ZipScatter2.png)

Finally, we can pick the five zip codes from these regions that have the highest forecast returns.

![RecommendedRegions](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/Recommendations.jpg)

![ExpectedReturns](https://github.com/calbal91/project-ARIMA-modelling/blob/master/Images/RecommendationReturns.jpg)