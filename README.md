
## Introduction
League of Legends is a multiplayer online battle arena developed by Riot Games in 2009. Over the years, the game has garnered worldwide attention, with tens of millions of active players on the game daily. In addition, the game's professional esports league have broke records with peaking viewership over the past couple of international tournaments. The dataset that I'll be using for this analysis will be esports data from 2025 which is taken from the Oracle's Elixir website.

With the introduction of Fearless draft, team compositions and strategies have taken the game to new heights. This dataset reveals several different statistics from all of the professional games that were played in 2025, such as winning teams, teamkills, and more. One metric I'm looking to expand upon is action.

In each match that is played, depending on the team composition, the audience may sit through two kinds of a games, a slow-paced game or a fast-paced one. With a slow-paced game, the primary focus for a team is to farm and scale and ultimately win through make strategic plays. However, a fast-paced game, the focus is on pure aggression and skirmishes especially early on to end the game as quickly as possible. When it comes to these two types of games, they both revolve around **action**.

The question I want to try and look at is **Looking at tier-one professional leagues, which league has the most "action-packed" games? Is the amount of "action" in this league significantly different than in other leagues?** To try and answer this question, I will be taking several ingame metrics to compute how "action-packed" matches are and compare them among different tier-one professional leagues. As for my predictive model, I will be looking to predict if a team won the game based on these action related metrics.

### Rows and Columns
In this dataset, there are over 100000 rows and 164 columns. For this specific question, we will only need a fraction of those columns to answer out question. Some of these key columns are:
* league: This column distinguishes which league the individual team is from. It will be used when we need to filter which league is tier 1.
* gamelength: This column determines how long each game took which will be used to help evaluate our "action" score.
* dpm (damage per minute): This column determines how much damage each team dished out per minute throughout the entire game. DPM is important in determining action-packness because the more damage that is dealt, the more fights there are throughout the game.
* dragons: This columns determines how many 'dragons' that the team has slain. Dragons are an objective that gives permanent buffs to the team for the remainder of the game. This plays a role in computing action since we have to think about the fights that take place to secure the objective between both teams.
* barons: This column determines how many 'barons' that the team has slain. Baron is an objective that gives a short buff to your team which includes an increase in damage, recalls, and makes your minions stronger. This objective is usually taken to either extend your lead even further, or it's taken to completely turn the game around.
* teamkills: This column tracks the total amount of kills each team totalled throughout the entire game.
* teamdeaths: This column tracks the total amount of deaths each team got during that game.
* vspm (vision score per minute): Vision score per minute tracks how much vision the team influenced throughout the game. Information is key since most of the map is shrouded in darkness. Being able to place down wards and get information is crucical especially when setting up fights and objectives.
* result: This column tracks whether the team won or lost which will be used in our classification model.
* participantid: This column tracks the id of each player and allows us to distinguish which person is which. There is also a special value (100 or 200) that gives us the cumulative statistics of each team.

## Data Cleaning and EDA
### Data Cleaning
When it came to cleaning the data, I decided to go with the columns: league, gamelength, dpm, dragons, barons, teamkills, teamdeaths, vspm, and result. When it came to missing values, there were no missing values found in these columns so I was able to leave it alone. However, I made new columns which combined some columns together. For instance, I made a objective column which combined barons and dragons together and a column called kdcombined which summed up the teamkills and teamdeaths together because I believed that combining both the kills and deaths would generate a better action score. Additionally, I also converted my gametime into minutes to make it easier to encode later on. 

Here is the head of the game_summary dataframe:

|   dragons |   barons |   gamelength |   teamkills |   teamdeaths | league   |   participantid |     dpm |   vspm |   result |   obj | kdcombined |   num_games |   gamelength_to_minutes |
|----------:|---------:|-------------:|------------:|-------------:|:---------|----------------:|--------:|-------:|---------:|------:|-------------:|------------:|------------------------:|
|         4 |        1 |         2123 |          17 |            5 | LPL      |             100 | 2241.45 | 9.3547 |        1 |     5 |           22 |           1 |                      21 |
|         1 |        0 |         2123 |           5 |           17 | LPL      |             200 | 2118.68 | 7.9416 |        0 |     1 |           22 |           1 |                      21 |
|         2 |        0 |         1952 |          11 |           18 | LPL      |             100 | 2290.76 | 7.1926 |        0 |     2 |           29 |           1 |                      19 |
|         2 |        1 |         1952 |          18 |           11 | LPL      |             200 | 2767.19 | 8.3299 |        1 |     3 |           29 |           1 |                      19 |
|         3 |        0 |         2014 |          18 |           22 | LPL      |             100 | 2824.38 | 9.5929 |        0 |     3 |           40 |           1 |                      20 |

### Univariate Analysis
<iframe
  src="assets/Univariate Graph (Teamkills).html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For my univariate analysis, I created a collection of plots which observed the distribution of teamkills for the different Tier 1 professional leagues. Looking at the distribution of teamkills in each plot, they seem to follow an rough bimodal distribution amongst all leagues. They tend to vary around 5-10 kills and 15-20 kills.
### Bivariate Analysis
<iframe
  src="assets/Bivariate VSPM vs Result.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For my bivariate analysis, I looked vision score per minute which is one of my action score parameters and compared that to result using a boxplot. The plot shows that the vision score per minute is much higher in terms of its lower and upper bound for the winning team compared to the losing team. 

### Interesting Aggregates
An interesting aggregate that I performed on my data was looking at the mean() and sum() of each league. The aggregate below depicts the mean() values of the games found in different Tier 1 leagues

| league   |   participantid |   gamelength |     dpm |   dragons |   barons |   teamkills |   teamdeaths |    vspm |   result |     obj |   kdcombined |   num_games |   gamelength_to_minutes |
|:---------|----------------:|-------------:|--------:|----------:|---------:|------------:|-------------:|--------:|---------:|--------:|-------------:|------------:|------------------------:|
| LCK      |             150 |      1935.74 | 2635.31 |   2.2036  | 0.524324 |     14.4045 |      14.4315 | 8.46914 |      0.5 | 2.72793 |      28.836  |           1 |                 18.8721 |
| LCP      |             150 |      1938.56 | 2562.58 |   2.19931 | 0.517182 |     13.9227 |      13.9467 | 8.31289 |      0.5 | 2.71649 |      27.8694 |           1 |                 18.9038 |
| LEC      |             150 |      2015.48 | 2540.96 |   2.38889 | 0.620915 |     13.5425 |      13.567  | 8.36914 |      0.5 | 3.0098  |      27.1095 |           1 |                 19.6797 |
| LPL      |             150 |      1948.12 | 2637.98 |   2.20807 | 0.53913  |     15.0447 |      15.0702 | 8.41569 |      0.5 | 2.7472  |      30.1149 |           1 |                 18.9764 |
| LTA      |             150 |      1993    | 2705.57 |   2.2125  | 0.5375   |     14.9875 |      14.9875 | 8.15228 |      0.5 | 2.75    |      29.975  |           1 |                 19.35   |

Some notable things that I found from this aggregate was that the LTA has more damage per minute compared to the rest of the leagues, but has the least games out of everyone else when looking at the aggregate dataframe using sum(). Additionally, I found that on average, the LPL typically has more kills and deaths combined compareed to the rest of the other leagues which might be an indicator or more aggression.

## Assessment of Missingness
### NMAR Analysis
One column that I believe is NMAR was the firstbaron column. The reason I believe that it's NMAR is because the game might already be decided long before the baron can actually be spawned or attempted. In this year's 2025 season, the time at which baron would spawn was pushed from 20 minutes to 25 minutes. Even if the baron spawns at 25 minutes, we still also have to consider if they actually attempteed to kill the objective depending on the state of the game. Additional data to attempt to push this to MAR would be the gamelength column which only resolves the first problem of the baron not spawning pre-25 minutes. One proposal I would add on would be to fill in those NaNs with 0's regardless of time since techncially, no team has taken a "first baron" at all.

### Missingness Dependency
The missiningness I decided to look at was the firstbaron column and I compared it to gamelength and result and tested it under a 0.05 significance level. 
For the first column I decided to test, I used gamelength because using prior knowledge, I would assume that the missingness was due to the fact that baron spawns way later into the game and since games might end below that time frame, then that would be a possible reason behind the missingness.

The hypotheses that I tested using a permutation test was:

**Null Hypothesis:** The distribution of gamelength when firstbaron is missing is the same as the distribution of gamelength when firstbaron is not missing

**Alternative Hypothesis:** The distribution of gamelength when firstbaron is missing is the not the same as the distribution of gamelength when firstbaron is not missing

The test statistic I used when performing this permutation test was difference in group means and had an observed statistic of 0.0259 with a p-value of 0.146. Since our significance level is 0.05, we fail to reject the null hypothesis meaning that our firstbaron missingness does not depend on gamelength. Down below is my empirical distribution for this permutation test.
<iframe
  src="assets/fbaron vs gamelength.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The other column I wanted to track was result. 
The hypotheses that I tested using a permutation test was:

**Null Hypothesis:** The distribution of result when firstbaron is missing is the same as the distribution of result when firstbaron is not missing

**Alternative Hypothesis:** The distribution of result when firstbaron is missing is the not the same as the distribution of result when firstbaron is not missing

For this column, I used TVD as my test statistic and got an observed statistic of 1.0 and a p-value of 1. With this conclusion, we fail to reject the null hypothesis meaning that our firstbaron missingness does not depend on result. Down below is the empirical distribution for this permutation test. 

<iframe
  src="assets/fbaron vs result.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing
For my hypothesis test, I wanted to obsserve the two best leagues in the world which are China's LPL and Korea's LCK. The hypothesis test I will be conducting will be looking at the "action-packedness" distribution between the two leagues. The point of this test is to see between which league is the most engaging and exciting to watch. The test statistic I will ultimately be using is the difference in group means with a significance level of 0.05.

**Null Hypothesis**: The action-packedness between the LCK and LPL have the same distribution and the observed difference in action score are due to random chance.

**Alternative Hypothesis**: The LCK has lower action-packedness than LPL on average. The observed difference cannot be explained by random chance alone.

When defining action-packedness, I decided to create a metric called "action-score" which consists of a combination of different columns from the game_summary data. I decided the weights on what I deemed as most important. The columns include: gamelength, kdcombined, obj, dpm, and vspm. To calculate my action-score, I also standardized the other columns to bring all the aforementioned metrics to the same scale. 

The Observed statistic that I got was ~6.79 and the p-value that I got was 0.038. This means that I reject the null hypothesis, indicating that there is some significant evidence to show that LCK may have a lower action-packedness compared to the LPL.

<iframe
  src="assets/LCK vs LPL.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


## Framing a Prediction Problem
Based on my action-based metrics from past sections, I wanted to see whether I can predict if a team will win or lose a match given those parameters. This prediction task will be a binary classification task where my true labels will be based on the column `results`. The metric I'll use will be accuracy because the data is roughly balanced as there are no ties meaning the outcomes are 50/50.

## Baseline Model
For my baseline model, I went with a Decision Tree classifier where I used the initial columns 'teamkills', 'teamdeaths', 'obj', and 'dpm' which are all qualitative columns. The train/test split I decided to use was an 80%/20% split. I also trained the model with a max_depth of 6 where I got a training accuracy of 0.974 and a test accuracy of 0.9537 which is a very high baseline. As a baseline model, I think it's already very strong and could pass as a final model. 

## Final Model
For my final model, I added the other action-score metrics such as 'gamelength_to_minutes' and 'vspm' and decided to change my Decision Tree into a Random Forest. I chose these two features to add on to my final model since it was only normal to use all of my features that I used to calculate my action score to find the end result. Additionally, for my 'vspm' after analyzing it in my previous bivariate analysis, I felt it was a good metric to put on since the higher the vision score meant the higher chance of winning. I also used GridSearchCV to find my most optimal parameters and was able to conclude that the best hyperparameters include a max_depth of 10, and n_estimators of 500.

The final score that I got was 0.9612 for my testing accuracy which is a small improvement from the baseline model, but my training accuracy was able to spike to 0.997 which is very close to perfect.

Here is a confusion matrix that evaluated my model's performance. 

## Fairness Model
