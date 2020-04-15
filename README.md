# Explaining The 2019 UK General Election


## The Objective

Analytically identify the drivers for the result of the 2019 UK General Election.

# Table of contents

   * [The Motivation](#the-motivation)
   * [The Technologies Used](#the-technologies-used)
   * [The Process Overview](#the-process-overview)
   * [Data Gathering](#data-gathering)
       * [Intro to UK Politics](#intro-to-uk-politics)
       * [Data Sources](#data-sources)
       * [EDA](#eda)
   * [Clustering](#clustering)
   * [Modelling](#modelling)
       * [The Target Variable](#the-target-variable)   
       * [Algorithms Used](#algorithms-used)
       * [Permutation Importance](#permutation-importance)
   * [Conclusion](#conclusion)


# The Motivation

On September 2nd, Boris Johnson, the UK’s Prime Minister, stood on the steps of 10 Downing Street and announced that he didn’t want a general election. Anyone with an ounce of political nous knew what was coming next.

On the morning of Friday the 13th (naturally) of December, the country awoke to find that it had given Boris Johnson the biggest Conservative election win since the days of Margaret Thatcher.

![BorisJohnson](https://github.com/calbal91/project-understanding-elections/blob/master/Images/boris.jpg)

Many places voted for the Conservatives for the first time, with the opposition Labour party suffering its worst defeat since before WW2. Could this result have been predicted? And what were the greatest factors that caused such a result?

Understanding these things could help Labour avoid such a grand-scale defeat in the future (or help the Conservatives maintain a compelling majority when the next election comes).


# The Technologies Used

* Pandas for data munging
* XGBoost, and Scikit-learn Random Forest / Decision Tree classifiers for building classification engines
* Scikit-learn GridSearchCV for hyperparameter tuning
* eli5 for Permutation Importance
* Matplotlib and Plotly for data visualisation


# The Process Overview

1. If we expect a model to tell us which factors were the most important in the election, we need to find some factors for it to choose from. In particular, we should scrape / download as much constituency-level data as possible from official government sources as we can.

2. Since this analysis was conducted in the week running up to the general election, we couldn't use the actual results as our target variable. Instead, we used the results of YouGov’s MRP Poll published on November 27th (just over two weeks before polling day). MRP (Multilevel Regression with Poststratification) is an advanced polling methodology, and the YouGov poll in question had a huge sample size, with over 100,000 people responding in the space of seven days. It was therefore able to give a projected vote share for each party in every constituency. In the event, it slightly underestimated the Conservative success in seat terms, though its projection of overall vote share was within 1–2% of the final result for the main parties.

3. Having built a dataset, we train classifier models. We then take the most accurate models, and perform permutation importance on them to see which features were most decisive. We can infer that these were the most critical factors in the election result.


# Data Gathering

The data gathering process for this project was significant. The dataset had over 100 features for each constituency in England and Wales, with most features covering all UK constituencies.

### Intro to UK Politics

Political power in the UK lies in the House of Commons (or Parliament). This is populated by 650 members (MPs), one from each of the UK’s 650 ‘constituencies’.

In a general election, each constituency votes for a single MP from a selection of candidates. The one candidate with the most votes in that constituency is elected as an MP. The political party that has the most elected MPs overall wins the election, and typically forms the government. This system is known as ‘First Past The Post’ (FPTP).

**The 2019 results - each hex represents one constituency

![2019Results](https://github.com/calbal91/project-understanding-elections/blob/master/Images/2019map.png)

This is a voting system that unambiguously helps the UK’s two main parties — the Conservatives and Labour, who have won every general election since 1922.

Another phenomena that arises from FPTP is ‘safe seats’ — constituencies that are almost certain to vote a given way. In the run up to the 2019 election, there were 170 constituencies that have voted for the same party since World War 2 (the constituency of North Shropshire is probably the safest seat of all — it has been held by the Conservatives since 1834).

This idea of 'safe seats', and seats that are closely faught ('marginals' in the lingo) will be important later.

**The main parties, represented on the political compass, as at the 2019 election

![PoliticalCompass](https://github.com/calbal91/project-understanding-elections/blob/master/Images/compass.png)

### Data Sources

There exists no single 'master' database of different constituency statistics that is publically available. Happily, a lot of official data is available at a constituency level through government websites, in particular https://commonslibrary.parliament.uk/local-data/

We can pull various CSVs together to create a grand table of constituency-by-constituency KPIs across a range of metrics; from house prices, weekly wages, and population density, to educational attainment, ethnicity, and even broadband quality.

#### The list of KPIs used as features in this project

![KPIs](https://github.com/calbal91/project-understanding-elections/blob/master/Images/kpis.png)

### EDA

# Clustering



# Modelling

### The Target Variable

As mentioned, the target variable for this analysis was the projected voting preference of each individual constituency, as forecast by YouGov's highly respected MRP Poll, published on November 27th (just over two weeks before the election itself).

It showed a harrowing picture for the opposition Labour party. A third of the seats that had previously been Conservative / Labour marginals now looked like they were safe Conservative, and up to 30% of seats that had been safe Labour in 2017 now looked as if they were going to be Conservative marginals.

By contrast, the Conservatives were set to lose very few safe seats, and they were still competitive in most seats that had previously been marginals.

**The list of KPIs used as features in this project

![Sankey](https://github.com/calbal91/project-understanding-elections/blob/master/Images/sankey.png)

### Algorithms Used

### Permutation Importance

The concept of permutation importance is very intuitive:

* Suppose we have a model, with a given loss function (for example, accuracy) of x
* Now, take a single column in the feature set, and shuffle the values for each of the datapoints
* Re-calculate the accuracy using this new dataset with the shuffled column
* Note how much the model’s accuracy suffered from this shuffling. This loss in accuracy is the ‘permutation importance’ of that feature
* Return the column to its un-shuffled state, and move onto the next feature — shuffling its values, and calculating how much the model’s accuracy falls

This procedure breaks the relationship between each feature and the target, thus the drop in accuracy is indicative of how much the model depends on that feature.


## Conclusion

