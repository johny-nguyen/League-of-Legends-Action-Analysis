
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

[INSERT DATAFRAME]


### Univariate Analysis

### Bivariate Analysis


## Assessment of Missingness
## NMAR Analysis
One column that I believe is NMAR was the firstbaron column. The reason I believe that it's NMAR is because the game might already be decided long before the baron can actually be spawned or attempted. In this year's 2025 season, the time at which baron would spawn was pushed from 20 minutes to 25 minutes. Even if the baron spawns at 25 minutes, we still also have to consider if they actually attempteed to kill the objective depending on the state of the game. Additional data to attempt to push this to MAR would be the gamelength column which only resolves the first problem of the baron not spawning pre-25 minutes. One proposal I would add on would be to fill in those NaNs with 0's regardless of time since techncially, no team has taken a "first baron" at all.


## Hypothesis Testing


## Framing a Prediction Problem


## Baseline Model


## Final Model


## Fairness Model
