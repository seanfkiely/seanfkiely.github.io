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

[The Elo rating system](https://en.wikipedia.org/wiki/Elo_rating_system) first began as a way to calculate the relative skill of chess players and can be applied to any players/teams that play zero-sum games, including [football](https://neilpaine.substack.com/p/2025-nfl-power-ratings-and-projections), [baseball](https://neilpaine.substack.com/p/2025-mlb-elo-power-ratings-playoff?open=false#%C2%A7mlb-elo-ratings-and-win-projections), and even [Pokémon battles](https://pokemonshowdown.com/pages/ladderhelp). 

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

I then scrape the  pages of MMA fighters gathering information on their weight class and gender and then scraping the results of their MMA fight records including outcome, opponent, date, and method of victory. For those interested in doing similar scraping, note that you may have to try different slugs at the end of a URL if there are multiple people with a Wikipedia page who have the same name (see e.g., [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva_(fighter)) the mixed martial artist and [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva) the soccer player) you can review my code for how I had this on the link to my Github page below.  Overall, I scrape about XXX total webpages and establish a dataset of over 44,0000 fights across XXX individual fighters. 

Note 10/29/25: Stopping for now. Next steps include talking about the total list of fighters, why soem fighters are not included (too obscure for a wikipedia page), defining the rating system including the traditional elo model, the decay function, and bonuses for fight finishes and championship fights/wins (refer to the elo notebook for reference). Then need to decide on final tables and visuals. Also mention that eventually I plan to create more results such as current and all-time elo ratings my division and gender.

## Elo Ratings
Maybe here do a small background on standard elo ratings

I create the Elo ratings as follows:

First, each fighter receives a baseline Elo rating of 1500 and I use a K factor of 44. When there is a bout, I obtain the expected score, or expected probability of Fighter A winning, $E_A$ based on the rating of Fighter A and Fighter B as:
$$
E_A = \frac{1}{1 + 10^{(R_B - R_A)/400}}
$$

I then define $S_A \in {0, 0.5, 1}$ if the result of the fight is a loss, draw, or win for Fighter A, respectively. Unlike most traditional Elo ratings, I also apply a decay function that accounts for fighter inactive. Decay functions in Elo ratings are commonly used to prevent individuals from protecting a high rankings by remaining inactive. For MMA, the use of a decay function benefits more active fighters and also penalizes fighters who are ducking fights (see e.g., Jon Jones vs. Tom Aspinall). However, some delays between fights can be due to injuries, scheduling issues, and fight cancellations. Therefore, I implement a conservative decay function. Fighter A's inactivity decay is defined as:

$$
\[

\]
$$

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

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_vs_win_rate.png" alt="ELO Rating vs Win Rate" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>ELO Rating vs Win Rate %</strong>
  </figcaption>
</figure>

<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/elo_trajectories.png" alt="ELO Trajectories of MMA Greats" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>ELO Trajectories of MMA Greats %</strong>
  </figcaption>
</figure>

</div>
