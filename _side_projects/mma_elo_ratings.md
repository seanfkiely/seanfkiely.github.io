---
title: "MMA Elo Ratings"
collection: side_projects
published: false
date: 2025-10-28
excerpt: "A rating system for mixed martial artists"
tags:
  - Web Scraping
  - Elo
  - Sports Analytics
  - Python
  - MMA
thumbnail: /images/fg_kicks_final.png
header:
  overlay_image: /images/fg_kicks_final.png
  overlay_filter: 0.4
  caption: "Apply Elo-style ratings to MMA"
---
<div class="page__content" markdown="1">
<body style="background-color:#f8f9fa;">
<div style="background:#fafafa; border:1px solid #eee; border-radius:10px; padding:2em; box-shadow:0 2px 4px rgba(0,0,0,0.05);" markdown="1">
<div style="font-family: 'Georgia', serif;" markdown="1">


#  An MMA Elo Rating System

This project uses fight outcomes and opponent skill level to derive an Elo rating system for mixed martial artists. From these ratings, I create tables and figures for best current fighter, best fighters of all time (MMA GOATs), best fights of all time, and more.

## Background

I used to closely follow mixed martial arts fights in organizations like the UFC, WEC, Strikeforce, etc. However, starting around 2020, my interest in the sport began to wane due to reasons including unequitable fighter pay and a stale product. However, my interest in #dataanalysis has only grown. I have long thought about applying a more rigorous rating system for fighters, as ratings and recent performance often determine who receives opportunities to fight for titles. The current ranking system in the UFC, for instance, is based on [UFC-selected media member rankings](/images/ufc_rankings_voters.png). Not exactly an unbiased sample of voters. This is when I had the idea to apply an Elo-based rating system to fighters to create a more unbiased list of the best mixed martial artists.

### Elo ratings

[The Elo rating system](https://en.wikipedia.org/wiki/Elo_rating_system) first began as a way to calculate the relative skill of chess players and can be applied to any players/teams that play zero-sum games, including [football](https://neilpaine.substack.com/p/2025-nfl-power-ratings-and-projections), board games like [Scrabble](https://wespa.org/aardvark/html/rankings/full_rankings.html), and even [Pokémon battles](https://pokemonshowdown.com/pages/ladderhelp). The main idea is that player quality is inferred from their performance against other players taking opponent quality into account. Each player in a match has an expected score, or probability of winning, and their ELO rating is based on the match outcome (win, loss, or draw), the expected score, and the maximum possible score per game (K-factor). The K-factor can be adjusted based on player quality or match frequency, but a fairly standard-sized K-factor for games played at high frequency is around 30. 

## Data Collection/Webscraping

To establish Elo ratings for mixed martial artists we need to obtain results of each fight (win, lose, draw, no contest, etc.), the opponent, the rankings of each fighter at the time of their bout, and optionally, as we will see, whether the fight was a finish (KO/TKO or submission) or a championship fight. Gathering this data is not a small feat, as, to my knowledge, no database currently exists that houses all this information. 

Therefore, to create this ranking system, I first start by scraping organizations' event pages, notable alumni, or current active rosters on Wikipedia to build a dataset of fighters. The list of MMA organizations (including those now defunct) includes:

1. UFC
2. Pride
3. WEC
4. Strikeforce
5. PFL
6. Bellator
7. Invicta
8. Rizin

I then scrape the  pages of MMA fighters, gathering data on: weight class, gender, and individual bout info such as outcome, opponent, date, and method of victory.[^1] Overall, I establish a dataset of nearly 45,000 fights across about 3,800 individual fighters. 

## Elo Ratings

In this section, I'll go into some detail defining how the ELO ratings are calculated. If you'd rather skip the math you can continue on to the result section and check out the tables and figures.

I create the Elo ratings as follows:

First, each fighter receives a baseline Elo rating. The model uses informative priors to assign the baseline rating. If a fighter has more than 10 professional fights, they receive a baseline rating of 1500. If the fighter has fewer than 10 fights but has a Wikipedia page, I take this to mean that the fighter is more notable and may be of higher quality. Their baseline thereofre ranges between 1300 and 1500 scaling linearly depending on the number of fights they've competed in. For fighters without a Wikipedia page and less than 10 points I use a baseline from 1100 and 1500 again sclaing linearly based on the number of fights completed. This allows ratings to adjust more realistically based on fighters' skill levels while also preventing fighters from "stat-padding" wins against lower ability fighters (see e.g., [Jeremy Horn](https://en.wikipedia.org/wiki/Jeremy_Horn)). The K-factor governs the sensitivity of the Elo updates, the higher the K-factor the more sensitive the ratings are to change. Games that can be played frequently may set lower K-factors such as 20 where multiple matches can be played in a single day. However, it is common in MMA to average about 2-4 fights per year. Therefore, I use a K-factor of 60. I also employ a dynamic K-factor that scales with opponents' experience. If a fighter faces an opponent with fewer than five fights the update is dampened, otherwise the update uses the full K value. This prevents large ratings jumps from wins over unproven fighters.

For each bout, I then obtain the expected score, or expected probability of Fighter A winning, $E_A$ based on the rating of Fighter A and Fighter B as:

$$
E_A = \frac{1}{1 + 10^{(R_B - R_A)/400}}
$$

where $R_A$ and $R_B$ are the current or baseline ratings of each fighter. I then define $S_A \in {0, 0.5, 1}$ if the result of the fight is a loss, draw, or win for Fighter A, respectively. Unlike most traditional Elo ratings, I also apply a decay function that accounts for fighter inactive. Decay functions in Elo ratings are used to prevent individuals from protecting a high ranking by remaining inactive. For MMA, the use of a decay function benefits more active fighters and also penalizes fighters who may duck fights (see e.g., Jon Jones vs. Tom Aspinall). However, some delays between fights can be due to injuries, scheduling issues, and fight cancellations. Therefore, I implement a very conservative exponential decay function toward a lower bound to account for inactivity. Fighter A's inactivity decay is defined as:

$$
R_{\text{new}} = R_{\min} + (R_{\text{old}} - R_{\min}) \cdot e^{-\ln(2)\cdot \frac{t}{t_{1/2}}}
$$

where $R_{\text{new}}$ is the fighters updated rating, $R_{\text{old}}$ is the previous rating, $R_{\text{min}}$ is the lower bound, $t$ is the number of days since Fighter A's last fight, and $t_{1/2}$ is the half life. I set $R_{\text{min}}$ to 1500 where fighters above 1500 are penalized for inactivity, but those below 1500 are not, and  $t_{1/2}$ to 1825. The figure below shows the decay in Elo rating due to inactivity with a half-life of 1825 days. This means that after 5 years of inactivity, the fighter's rating falls halfway between their current rating (1800) and the floor (1500). Again, this is a conservative decay function, meant to penalize fighters for purposeful inactivity with the caveat that inactivity can arise for multiple reasons outside of the fighters' control. Therefore, a fighter loses about 100 points for 3 years of inactivity. It is not a substantial drop in their rating, but it does effectively prevent fighters from sitting on their high rating and may more accurately reflect the fighter's ability over time. 

<p = align="center">
<figure style="text-align:center; margin: 2em 0;">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
  <strong>FIGURE 1: Decay Function</strong>
  </figcaption>
  <img src="/images/decay-function.png" alt="Decay Function" style="width:70%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
</figure>
</p>

Finally, I also provide two types of bonuses for wins: 1) finishes and 2) championships. Each fighter receives additional points for each victory that ends in a finish. This provides a small bump in Elo ratings to fighters who score a dominant win, but not overemphasizing finishes, as they are also positively correlated with weight class. Fighters also receive a bonus for winning a championship fight as it signals they are facing a higher quality opponent, score a victory in a higher stakes match, and may also have to compete in additional "championship" rounds. I give greater weight to fighters who win a championship in a high tier promotion (UFC, PRIDE, WEC, Strikeforce, or Bellator) and award a lower bonus to championships in other lower-tier promotions. This tiering system prevents those who dominate lower-tier organizations but infrequently face higher-quality competition from padding their rating (see e.g., [Hector Lombard's](https://en.wikipedia.org/wiki/H%C3%A9ctor_Lombard) multiple Cage FC title defenses and subsequent UFC run). 

The updated rating for Fighter A is then:

$$
R_{\text{new}} = R_a + K_a \cdot (S_a - E_a) + Bonus_{Finish} + Bonus_{Championship}
$$

where $R_{\text{new}}$ is Fighter A's new rating, $R_a$ is Fighter A's pre-fight rating, $K_a$ is the K-factor, $S_a$ is the score, $E_a$ is the win probability, and $Bonus_{Finish}$ and $Bonus_{Championship}$ are the additional bonuses. As an example, when Georges St. Pierre fought Jake Shields at UFC 129, St. Pierre's ELO rating was 1855 while Shields's was 1784. Therefore, GSP's expected score was 0.6, and as it was a championship fight in a high-tier promotion resulting in a unanimous decision win for GSP, he received the championship bonus but not the finish bonus. Using a championship bonus of 6, his new rating would be 1885.

## Results
After creating this dataset, there are so many interesting findings to explore. I supply some here, but feel free to ask for specific requests or try things out yourself! 

### Peak ELO Ratings
I start by creating a list of the peak ELO ratings achieved by any fighter in Table 1; this rating could be used as one proxy for determining the greatest fighters of all time (GOATs), but capturing sustained success is also an important metric. Fedor Emelianenko narrowly edges out Anderson Silva for peak ELO rating by less than four points. Both went on historic runs but Fedor holds on to the top spot after notable victories over legends of the sport like Antônio Rodrigo Nogueira (#12) and Mirko Cro Cop (#35). Islam Makhachev also cracks the top five peak ratings after his dominant performance over Jack Della Maddalena at UFC 322. 

I speculate that the rise of champion vs champion fights across weight classes, starting in 2016 when Conor McGregor moved up to fight for the lightweight belt against Eddie Alvarez, has contributed to some rating inflation. Eleven of the top twenty-one fighters and three of the top five fighters by peak ELO rating have been involved in one of these bouts. Henderson is the only fighter to have earned the "double champ" status from this list prior to 2016 (2007 in Pride). 

<div align = "center">

**TABLE 1. Peak ELO Ratings**

|   Rank | Fighter                  |   Peak ELO Rating |   Rank | Fighter              |   Peak ELO Rating |
|-------:|:-------------------------|------------------:|-------:|:---------------------|------------------:|
|      1 | Fedor Emelianenko        |           2077.17 |     16 | Dan Henderson        |           1942.77 |
|      2 | Anderson Silva           |           2073.81 |     17 | Donald Cerrone       |           1941.34 |
|      3 | Daniel Cormier           |           2056.81 |     18 | Israel Adesanya      |           1940.36 |
|      4 | Georges St-Pierre        |           2040.13 |     19 | Max Holloway         |           1937.65 |
|      5 | Islam Makhachev          |           2035.44 |     20 | Wanderlei Silva      |           1936.89 |
|      6 | Jon Jones                |           2021.89 |     21 | Dustin Poirier       |           1936.61 |
|      7 | Kamaru Usman             |           1993.51 |     22 | Robbie Lawler        |           1932.88 |
|      8 | José Aldo                |           1988.46 |     23 | Matt Hughes          |           1931.98 |
|      9 | Charles Oliveira         |           1982.92 |     24 | Chuck Liddell        |           1929.73 |
|     10 | Gegard Mousasi           |           1968.75 |     25 | Fabrício Werdum      |           1927.55 |
|     11 | Stipe Miocic             |           1967.85 |     26 | Benson Henderson     |           1924.99 |
|     12 | Antônio Rodrigo Nogueira |           1962.84 |     27 | Cain Velasquez       |           1922.82 |
|     13 | Alexander Volkanovski    |           1962.73 |     28 | Valentina Shevchenko |           1916.61 |
|     14 | Ryan Bader               |           1960.35 |     29 | Khabib Nurmagomedov  |           1915.37 |
|     15 | Demetrious Johnson       |           1946.36 |     30 | Merab Dvalishvili    |           1914.8  
</div>

This metric also correlates reasonably well with consensus picks for greatest fighters which usually include Fedor, Silva, St. Pierre, etc. One notable fighter who ranks lower in my ELO rating system than in most rankings is Khabib Nurmagomedov. While Khabib tore through the UFC lightweight division, eventually capturing the belt and retiring undefeated, he barely cracks the top 30 list here. In Figure 1, I plot the ELO Ratings over time of Khabib and some of the other consensus greatest fighters. While Khabib's rating dropped somewhat between 2014 and 2016 due to a layoff from injuries and one bout cancellation (the first of five attempts to schedule the infamous never-to-be fight between Nurmagomedov and Tony Ferguson), Khabib's rating is mostly stunted due to a string of fights against lesser competition early in his career. In his first sixteen professional fights, he only faced two fighters notable enough to have their own Wikipedia page and twelve of which never competed in more than five professional fights. These opponents had a combined record of 47-51-1 and excluding the two "notable" fighters, they had a combined record of 7-38. Given that these lower-quality matches make up over half of Khabib's career fights, his peak rating seems much more reasonable.

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_trajectories.png" alt="ELO Trajectories of MMA Greats" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>FIGURE 1: ELO Trajectories of MMA Greats</strong>
  </figcaption>
</figure>

Table 2 lists the current best fighters by ELO rating. Note that I only include fighters who have been active in the last two years. Here we see Islam Makhachev at the top of the heap. Makhachev has been on a tremendous string of wins and increased his ELO rating by 161 points after beating perennial favorites in Charles Oliveira (9th peak all-time ELO at the time of fight), Alexander Volkanovski (13th peak all-time ELO), and Dustin Poirier (21st). We will see if he can extend his lead in the rankings by facing current lightweight champion Ilia Topuria (#7) who has been calling for a match with Makhachev. Valentina Shevchenko, Zhang Weili, and Cris Cyborg are also the top three women who enter the list. In the near future, I plan to extend this analysis to WMMA and create current rankings by weight class for both women and men.

<div align = "center">

**TABLE 2. Best Current Fighters by ELO Rating**

|   Rank | Fighter               |   ELO Rating |   Rank | Fighter           |   ELO Rating |
|-------:|:----------------------|-------------:|-------:|:------------------|-------------:|
|      1 | Islam Makhachev       |      2035.44 |     16 | Shavkat Rakhmonov |      1851.42 |
|      2 | Max Holloway          |      1927.85 |     17 | Jiří Procházka    |      1848.28 |
|      3 | Charles Oliveira      |      1917.56 |     18 | Alexander Volkov  |      1847.14 |
|      4 | Valentina Shevchenko  |      1916.61 |     19 | Ryan Bader        |      1841.42 |
|      5 | Merab Dvalishvili     |      1914.8  |     20 | Alexandre Pantoja |      1840.76 |
|      6 | Justin Gaethje        |      1889.06 |     21 | Kamaru Usman      |      1840.54 |
|      7 | Ilia Topuria          |      1882.59 |     22 | Zhang Weili       |      1838.97 |
|      8 | Alexander Volkanovski |      1878.22 |     23 | Khamzat Chimaev   |      1835.12 |
|      9 | Alex Pereira          |      1871.46 |     24 | Cris Cyborg       |      1833.32 |
|     10 | Jon Jones             |      1864.55 |     25 | Vadim Nemkov      |      1828.87 |
|     11 | Dricus du Plessis     |      1854.68 |     26 | Dustin Poirier    |      1828.13 |
|     12 | Magomed Ankalaev      |      1853.91 |     27 | Corey Anderson    |      1824.57 |
|     13 | Arman Tsarukyan       |      1853.06 |     28 | Movsar Evloev     |      1814.27 |
|     14 | Aljamain Sterling     |      1852.78 |     29 | Francis Ngannou   |      1808.74 |
|     15 | Belal Muhammad        |      1851.51 |     30 | Ciryl Gane        |      1808.23 |
</div>

I also sum the ELO ratings of fighters and their opponents to create a list of the top fights of all time in Table 3. By Elo rating, the trilogy fight between Daniel Cormier and Stipe Miocic tops the list. Cormier is involved in five of the top ten fights on the list with his first fight against Jon Jones coming in at #7 and his 2nd fight against Anothy Johnson at #9. Cormier vs Jones 2 does not crack the top 10 as it was ruled a no contest (ommitted from the dataset) after Jones failed a drug test.

<div = "center">

**TABLE 3. Greatest Fights by ELO Rating**
  
|   Rank | Fighter 1             |   ELO Rating 1 | Fighter 2         |   ELO Rating 2 |   Combined ELO Rating |
|-------:|:----------------------|---------------:|:------------------|---------------:|----------------------:|
|      1 | Daniel Cormier        |        2053.23 | Stipe Miocic      |        1890.61 |               3943.84 |
|      2 | Stipe Miocic          |        1935.6  | Daniel Cormier    |        2002.63 |               3938.24 |
|      3 | Stipe Miocic          |        1926.22 | Daniel Cormier    |        1992.24 |               3918.46 |
|      4 | Jake Shields          |        1894.11 | Georges St-Pierre |        1997.36 |               3891.48 |
|      5 | Chael Sonnen          |        1845.49 | Anderson Silva    |        2045.23 |               3890.71 |
|      6 | Alexander Volkanovski |        1950.65 | Islam Makhachev   |        1933.72 |               3884.38 |
|      7 | Jon Jones             |        1994.77 | Daniel Cormier    |        1888.75 |               3883.52 |
|      8 | Dustin Poirier        |        1898.04 | Islam Makhachev   |        1973.18 |               3871.22 |
|      9 | Daniel Cormier        |        1956.09 | Anthony Johnson   |        1912.28 |               3868.37 |
|     10 | Andrei Arlovski       |        1813.39 | Fedor Emelianenko |        2051.75 |               3865.14
</div>

In Figure 2, I plot the current ELO ratings and fighters' win rate percentages. Overall, we see a positive correlation between win rate and ELO rating. On the bottom left corner of the figure, we see lower-level fighters such as WWE star turned MMA wannabe CM Punk, moving up and to the right we star to see fighters like Clay Guida who are "gatekeepers" of their weight classes, high level fighters that never quite made it to the upper echelon. Beyond that, we see elite-level fighters like Demetrious Johnson and Ilia Topuria (and a bit lower we have Dada 5000 with his uhh *interesting* fight with Kimbo Slice).  

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_vs_win_rate.png" alt="ELO Rating vs Win Rate" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>FIGURE 2: ELO Rating vs Win Rate %</strong>
  </figcaption>
</figure>

Figure 4 plots the top 10 ELO ratings over time using a bar chart race. While the first recorded MMA match was in 1980 (Rei Zulu vs. Rickson Gracie), fights were relatively uncommon (five fights in the 1980s) until 1993, when both Pancrase and the UFC held their first events. Therefore, this bar chart begins in 1993, updating the top 10 ELO ratings every month thereafter. 

<p align="center">
  <img src="/images/elo_race_small.gif" width="850">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>FIGURE 3: ELO Ratings Since 1993</strong>
  </figcaption>
</p>

## Code
If you'd like to explore this dataset more, you can find the data and code here.

[^1]: For those interested in doing similar scraping, note that you may have to try different slugs at the end of a URL if there are multiple people with a Wikipedia page who have the same name (see e.g., [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva_(fighter)) the mixed martial artist and [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva) the soccer player) you can review my code for how I handle this on the link to the Github folder.
