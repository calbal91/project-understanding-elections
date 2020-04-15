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
       * [Brexit](#brexit)
   * [Clustering](#clustering)
   * [Modelling](#modelling)
       * [The Target Variable](#the-target-variable)   
       * [Algorithms Used](#algorithms-used)
       * [Permutation Importance](#permutation-importance)
       * [Important Features](#important-features)
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

**The 2019 results - each hex represents one constituency**

![2019Results](https://github.com/calbal91/project-understanding-elections/blob/master/Images/2019map.png)

This is a voting system that unambiguously helps the UK’s two main parties — the Conservatives and Labour, who have won every general election since 1922.

Another phenomena that arises from FPTP is ‘safe seats’ — constituencies that are almost certain to vote a given way. In the run up to the 2019 election, there were 170 constituencies that have voted for the same party since World War 2 (the constituency of North Shropshire is probably the safest seat of all — it has been held by the Conservatives since 1834).

This idea of 'safe seats', and seats that are closely faught ('marginals' in the lingo) will be important later.

**The main parties, represented on the political compass, as at the 2019 election**

![PoliticalCompass](https://github.com/calbal91/project-understanding-elections/blob/master/Images/compass.png)

### Data Sources

There exists no single 'master' database of different constituency statistics that is publically available. Happily, a lot of official data is available at a constituency level through government websites, in particular https://commonslibrary.parliament.uk/local-data/

We can pull various CSVs together to create a grand table of constituency-by-constituency KPIs across a range of metrics; from house prices, weekly wages, and population density, to educational attainment, ethnicity, and even broadband quality.

#### The list of KPIs used as features in this project

![KPIs](https://github.com/calbal91/project-understanding-elections/blob/master/Images/kpis.png)

### EDA

It can be eye opening to investigate the geographical distribution of some of these KPIs, and the regional disparities that they demonstrate.

For example, we see that wages are higher in constituencies in London and the South East, which should be no surprise to any UK resident. However, house prices have grown so much in these places compared to the rest of the UK, that the average property is worth up to 30 times the annual wage (compared with 5–10 times elsewhere).

![Wages](https://github.com/calbal91/project-understanding-elections/blob/master/Images/wages.png)

Where you live also correlates strongly with the type of industry you’re likely to work in.

![Industries](https://github.com/calbal91/project-understanding-elections/blob/master/Images/industry.png)

Blue collar work (for example, heavy industry and hospitality) is more prevalent in the Midlands and North of England — white collar industries (financial services, IT, and sciences) are clustered in cities, especially London. Constituencies with more white collar workers have higher wages, and lower unemployment.

![IndustryScatter](https://github.com/calbal91/project-understanding-elections/blob/master/Images/industryscatter.png)

This ‘London vs. The Rest’ narrative runs through much of the data; Londoners tend to be better qualified, more ethnically diverse, and more international than the rest of the country.

![Qualifications](https://github.com/calbal91/project-understanding-elections/blob/master/Images/qualifications.png)

![Immigration](https://github.com/calbal91/project-understanding-elections/blob/master/Images/immigration.png)

### Brexit

These factors all come into play when we think about the great bogeyman of current British politics: Brexit. 

The first thing to note is the diversity of opinion that Brexit generates. Though the national margin of victory was very narrow, at 52% vs 48%, this vote spread was not evenly across constituencies. The leave vote was as low as 20% in some areas, but as high as 75% in others.

![BrexitVote](https://github.com/calbal91/project-understanding-elections/blob/master/Images/brexitvote.png)

Unsurprisingly, this spread appears to be dependent on geography. We note strong Leave votes outside of London — mostly in middle England.

![BrexitMap](https://github.com/calbal91/project-understanding-elections/blob/master/Images/brexitmap.png)

The drivers for Brexit were complex and diverse, however, there are some individual features that correlate very strongly with a constituency’s leave vote — qualification levels, and industry.

![BrexitScatter](https://github.com/calbal91/project-understanding-elections/blob/master/Images/brexitscatter.png)

The correlation coefficient between the share of leave votes in a constituency and the share of people with level 4+ qualifications (degrees or equivalent) is very negative, at -0.72, whereas the coefficient between the share of leave votes and the share of people working in heavy industry is very high, at +0.72.


# Clustering

One application of the data is Unsupervised Learning. In other words, without labelling the constituencies by their voting intention, can we cluster them simply based on their demographics?

A use case of this is outlined below.

![Clustering](https://github.com/calbal91/project-understanding-elections/blob/master/Images/clustering1.jpg)

![Clustering](https://github.com/calbal91/project-understanding-elections/blob/master/Images/clustering2.jpg)


# Modelling

### The Target Variable

As mentioned, the target variable for this analysis was the projected voting preference of each individual constituency, as forecast by YouGov's highly respected MRP Poll, published on November 27th (just over two weeks before the election itself).

It showed a harrowing picture for the opposition Labour party. A third of the seats that had previously been Conservative / Labour marginals now looked like they were safe Conservative, and up to 30% of seats that had been safe Labour in 2017 now looked as if they were going to be Conservative marginals.

By contrast, the Conservatives were set to lose very few safe seats, and they were still competitive in most seats that had previously been marginals.

**The forecasted change of seat types compared to the 2017 election**

![Sankey](https://github.com/calbal91/project-understanding-elections/blob/master/Images/sankey.png)

It's worth noting the massive class imbalances we have here. To combat this, we will use the SMOTE algorithm to generate synthetic datapoints for the minority classes.

Note, since SMOTE uses a ‘nearest neighbour’ algorithm, we can not synthesise for classes where there was only one datapoint to begin with. We will therefore drop such classes from our analysis going forward (these only represent half a percent of the seats in parliament, so this shouldn’t affect the analysis unduly).


### Algorithms Used

### Permutation Importance

The concept of permutation importance is very intuitive:

* Suppose we have a model, with a given loss function (for example, accuracy) of x
* Now, take a single column in the feature set, and shuffle the values for each of the datapoints
* Re-calculate the accuracy using this new dataset with the shuffled column
* Note how much the model’s accuracy suffered from this shuffling. This loss in accuracy is the ‘permutation importance’ of that feature
* Return the column to its un-shuffled state, and move onto the next feature — shuffling its values, and calculating how much the model’s accuracy falls

This procedure breaks the relationship between each feature and the target, thus the drop in accuracy is indicative of how much the model depends on that feature.

### Important Features


## Conclusion

The Conservatives were better placed to run a focussed campaign that would still appeal to a large number of constituencies. Across the most 'important' features, Conservative/Labour marginal seats looked much more similar to existing 'safe' Conservative seats.

Labour, however, were forced to appeal to a very wide range of voters. This came across in their campaign, which was criticised as being muddled and too wide-ranging.

Uniting the various types of voter that might support Labour looks like a difficult task. If nothing else, it would take seriously talented leadership, and there is significant evidence to suggest that Labour was sorely lacking this in the 2019 election. There is a sea of anecdotal evidence that Jeremy Corbyn, their leader, was profoundly unpopular on the doorstep during the campaign. More scientific polling since the election seems to bear this out.

![OpiniumPolling](https://github.com/calbal91/project-understanding-elections/blob/master/Images/opiniumpolling.jpg)

Source: https://twitter.com/OpiniumResearch/status/1205510937995812864
