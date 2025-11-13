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

This project uses fight outcomes and opponent skill level to derive an Elo rating system for mixed martial artists. From these ratings, I create tables and figures for best current fighter, best fighters of all time (MMA GOATs), best fights of all time based on these ratings, and more.

## Background

I used to follow mixed martial arts fights in organizations like the UFC, WEC, Strikeforce, etc. very closely. However, starting around 2020, my interest in the sport began to wane, partly because the UFC (a near monopoly now) has continued to put out a stale product. However, my interest in #dataanalysis has only strengthened. I have long thought about applying a more rigorous rating system for fighters as ratings and recent performance often determine who receives opportunities to fight for titles. The current ranking system in the UFC for instance is based on UFC-selected media member rankings. Not exactly an unbiased sample of voters. This is when I had the idea to apply an Elo-based rating system to fighters to create a more unbiased list of the best mixed martial artists.

### Elo ratings

[The Elo rating system](https://en.wikipedia.org/wiki/Elo_rating_system) first began as a way to calculate the relative skill of chess players and can be applied to any players/teams that play zero-sum games, including [football](https://neilpaine.substack.com/p/2025-nfl-power-ratings-and-projections), board games such as [Scrabble](https://wespa.org/aardvark/html/rankings/full_rankings.html), and even [Pokémon battles](https://pokemonshowdown.com/pages/ladderhelp). 

Note: Here I should probably add more stuff on the background of Elo ratings, but then save the calculations of the system for later.

## Data Collection/Webscraping

In order to establish Elo ratings for mixed martial artists we need to obtain results of each fight (win, lose, draw, no contest, etc.), the opponent, the rankings of each fighter at the time of their bout, and optionally, as we will see, whether the fight was a finish (KO/TKO or submission) or a championship fight. Gathering this data is not such a small feat as no database currently exists to my knowledge that houses all of this information. 

Therefore, to create this ranking system, I first start by scraping organization's event pages, notable alumni, or current active rosters on Wikipedia to build a dataset of fighters. The list of MMA organizations (including those now defunct) includes:

1. UFC
2. Pride
3. WEC
4. Strikeforce
5. PFL
6. Bellator
7. Invicta
8. Rizin

I then scrape the  pages of MMA fighters gathering information on their weight class and gender and then scraping the results of their MMA fight records including outcome, opponent, date, and method of victory. For those interested in doing similar scraping, note that you may have to try different slugs at the end of a URL if there are multiple people with a Wikipedia page who have the same name (see e.g., [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva_(fighter)) the mixed martial artist and [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva) the soccer player) you can review my code for how I handle this on the link to Github folder below.  Overall, I establish a dataset of nearly 45,000 fights across about 3,800 individual fighters. 

## Elo Ratings
Maybe here do a small background on standard elo ratings

I create the Elo ratings as follows:

First, each fighter receives a baseline Elo rating. The model uses informative priors to assign the baseline rating. If a fighter has a wikipedia page (notable fighter) or more than five professional fights, they recieve the high baseline rating of 1500, otherwise they receive the low baseline rating of 1000. This prevents fighters from receiving unrealistic initial ratings, and more importantly, prevents fighters from "stat-padding" wins against lower ability fighters (see e.g., [Jeremy Horn](https://en.wikipedia.org/wiki/Jeremy_Horn)). The K-factor governs the sensitivity of the Elo updates, the higher the K-factor the more sensitive the ratings are to change. Games that can be played frequently may set lower K-factors such as 20 as even multiple matches can be played in one day. However, it is common in MMA to average about 2-3 fights per year. Therefore, I use a K-factor of 60. I also employ a dynamic K-factor that scales with opponents' experience. If a fighter faces an opponent with fewer than five fights the update is dampened, otherwise the update uses the full K value. This prevents large ratings jumps from wins over unproven fighters.

For each bout, I then obtain the expected score, or expected probability of Fighter A winning, $E_A$ based on the rating of Fighter A and Fighter B as:

$$
E_A = \frac{1}{1 + 10^{(R_B - R_A)/400}}
$$

where $R_A$ and $R_B$ are the current or baseline ratings of each fighter. I then define $S_A \in {0, 0.5, 1}$ if the result of the fight is a loss, draw, or win for Fighter A, respectively. Unlike most traditional Elo ratings, I also apply a decay function that accounts for fighter inactive. Decay functions in Elo ratings are commonly used to prevent individuals from protecting a high ranking by remaining inactive. For MMA, the use of a decay function benefits more active fighters and also penalizes fighters who are ducking fights (see e.g., Jon Jones vs. Tom Aspinall). However, some delays between fights can be due to injuries, scheduling issues, and fight cancellations. Therefore, I implement a conservative exponential decay function toward a lower bound to account for inactivity. Fighter A's inactivity decay is defined as:

$$
R_{\text{new}} = R_{\min} + (R_{\text{old}} - R_{\min}) \cdot e^{-\ln(2)\cdot \frac{t}{t_{1/2}}}
$$

where $R_{\text{new}}$ is the fighters updated rating, $R_{\text{old}}$ is the previous rating, $R_{\text{min}}$ is the lower bound, $t$ is the number of days since Fighter A's last fight, and $t_{1/2}$ is the half life. I set $R_{\text{min}}$ to 1500 where fighters above 1500 are penalized for inactivity but those below 1500 are not and  $t_{1/2}$ to 3650. The figure below shows the decay in Elo rating due to inactivity with a half life od 3650 days. This means that after 10 years of inacitivity the fighter's rating falls halfway between their current rating (1800) and the floor (1500). Again this is a very conservative decay function, meant to penalize fighters for purposeful inactivity with the caveat that inactivity can arise for multiple reasons outside of the fighters control. Therefore, a fighter loses only about 50 points for inactivity over roughly a 3 year period. It is not a substantial drop in their rating but it does effectively prevent fighters from sitting on their high rating and may more accurately reflect the the fighter's ability over time. Finally, I also provide two types of bonuses for wins 1) finishes and 2) championships. Each fighter receives K-factor + 2 for each victory that ends in a finish. This provides a small bump in Elo ratings to fighters who score a dominant win, but not overemphasizing finishes as they are also positively correlated with weight class. Fighters also receive a bonus for winning a championship fight as it signals they are facing a higher quality opponent, score a victory in a higher stakes match, and may also have to compete in additional "championship" rounds. I give greater weight to fighters who win a championship in a high tier promotion (UFC, PRIDE, WEC, Strikeforce, or Bellator) and award a lower bonus to championships in other lower tier promotions. This tiering system prevents those who dominate lower tier organizations but infrequently face higher quality competition to pad their rating (see e.g., [Hector Lombard's](https://en.wikipedia.org/wiki/H%C3%A9ctor_Lombard) multiple Cage FC title defenses and subsequent UFC run). 

## Results
After creating this unique dataset, there are so many interesting findings to explore. I supply a few interesting ones here, but feel free to ask for specific requests or try things out yourself! I start out by creating a list of the peak ELO ratings achieved by any fighter in Table 1; this rating could be used as one proxy for the determination of "GOAT status", but capturing sustained success is an important metric too.

<div align = "center">

**TABLE 1. Peak ELO Ratings**

|   Rank | Fighter                  |   Peak ELO Rating |
|-------:|:-------------------------|------------------:|
|      1| Fedor Emelianenko        |           1921.85 |
|      2| Anderson Silva           |           1916.06 |
|      3| Georges St-Pierre        |           1897.82 |
|      4| Daniel Cormier           |           1894.29 |
|      5| Jon Jones                |           1886.37 |
|      6| Islam Makhachev          |           1875.23 |
|      7| Kamaru Usman             |           1867.22 |
|      8| Jose Aldo                |           1866.77 |
|      9| Antônio Rodrigo Nogueira |           1862.21 |
|      10| Alexander Volkanovski    |           1857.09 |
|      11| Wanderlei Silva          |           1848.46 |
|      12| Demetrious Johnson       |           1838.79 |
|      13| Charles Oliveira         |           1837.04 |
|      14| Benson Henderson         |           1835.63 |
|      15| Bas Rutten               |           1831.75 |
|      16| Ryan Bader               |           1829.18 |
|      17| Chuck Liddell            |           1828.39 |
|      18| Israel Adesanya          |           1827.1  |
|      19| Donald Cerrone           |           1826.88 |
|      20| Stipe Miocic             |           1824.66 |
|      21| Khabib Nurmagomedov      |           1823.15 |
|      22| Gegard Mousasi           |           1822.49 |
|      23| Cain Velasquez           |           1820.86 |
|      24| Mirko Filipović          |           1820.6  |
|      25| Max Holloway             |           1816.76 |
</div>

Table 2 lists the current best fighters by ELO rating. Note that I only include fighters who have been active in the last two years. Here we see Islam Makhachev at the top of the heap. Makhachev has been on a tremendous string of wins and increased is ELO rating by X points after beating perennial favorites in Charles Oliveira (13th peak all-time ELO at the time of fight), Alexander Volkanovski (10th peak all-time ELO), and Dustin Poirier.

<div align = "center">

**TABLE 2. Best Current Fighters by ELO Rating**

|   Rank | Fighter               |  ELO Rating |
|-------:|:----------------------|------------:|
|      1 | Islam Makhachev       |     1875.23 |
|      2 | Jon Jones             |     1828.93 |
|      3 | Merab Dvalishvili     |     1811.1  |
|      4 | Ilia Topuria          |     1784.32 |
|      5 | Alex Pereira          |     1778.97 |
|      6 | Zhang Weili           |     1776.12 |
|      7 | Valentina Shevchenko  |     1773.53 |
|      8 | Max Holloway          |     1765.56 |
|      9 | Francis Ngannou       |     1759.62 |
|      10 | Alexandre Pantoja     |     1757.39 |
|      11 | Khamzat Chimaev       |     1757.09 |
|      12 | Alexander Volkanovski |     1756.42 |
|      13 | Arman Tsarukyan       |     1755.86 |
|      14 | Dricus du Plessis     |     1754.7  |
|      15 | Shavkat Rakhmonov     |     1746.39 |
|      16 | Charles Oliveira      |     1745.71 |
|      17 | Jiří Procházka        |     1743.75 |
|      18 | Cris Cyborg           |     1743.31 |
|      19 | Magomed Ankalaev      |     1740.63 |
|      20 | Belal Muhammad        |     1738.91 |
|      21 | Movsar Evloev         |     1736.5  |
|      22 | Justin Gaethje        |     1733.76 |
|      23 | Ciryl Gane            |     1731.67 |
|      24 | Aljamain Sterling     |     1731.29 |
|      25 | Alexander Volkov      |     1724.6  |
</div>

I also sum the ELO ratings of fighters and their opponents to create a list of the top fights of all time in Table 3. 

<div = "center">

**TABLE 3. Greatest Fights by ELO Rating**
  
|   Rank | Fighter 1                |   ELO Rating 1 | Fighter 2         |   ELO Rating 2 |   Combined ELO Rating |
|-------:|:-------------------------|---------------:|:------------------|---------------:|----------------------:|
|      1 | Antônio Rodrigo Nogueira |        1852.85 | Fedor Emelianenko |        1801.35 |               3654.2  |
|      2 | Daniel Cormier           |        1839.88 | Stipe Miocic      |        1807.52 |               3647.41 |
|      3 | Mirko Filipović          |        1767.61 | Fedor Emelianenko |        1879.48 |               3647.09 |
|      4 | Daniel Cormier           |        1790.66 | Jon Jones         |        1855.94 |               3646.6  |
|      5 | Georges St-Pierre        |        1854.59 | Jake Shields      |        1784.2  |               3638.79 |
|      6 | Alexander Volkanovski    |        1821.39 | Islam Makhachev   |        1814.64 |               3636.03 |
|      7 | Fedor Emelianenko        |        1896.6  | Andrei Arlovski   |        1738.96 |               3635.56 |
|      8 | Chael Sonnen             |        1745.37 | Anderson Silva    |        1887.82 |               3633.19 |
|      9 | Daniel Cormier           |        1873.37 | Stipe Miocic      |        1759.51 |               3632.88 |
|     10 | Alexander Volkanovski    |        1842.15 | Islam Makhachev   |        1789.49 |               3631.64 |
</div>

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_vs_win_rate.png" alt="ELO Rating vs Win Rate" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>ELO Rating vs Win Rate %</strong>
  </figcaption>
</figure>

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_trajectories.png" alt="ELO Trajectories of MMA Greats" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>ELO Trajectories of MMA Greats</strong>
  </figcaption>
</figure>

<p align="center">
  <img src="/images/elo_animation.gif" width="850">
</p>
