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

## Data Collection

### Webscraping
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

I then scrape the  pages of MMA fighters gathering information on their weight class and gender and then scraping the results of their MMA fight records including outcome, opponent, date, and method of victory. For those interested in doing similar scraping, note that you may have to try different slugs at the end of a URL if there are multiple people with a Wikipedia page who have the same name (see e.g., [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva_(fighter)) the mixed martial artist and [Thiago Silva](https://en.wikipedia.org/wiki/Thiago_Silva) the soccer player) you can review my code for how I had this on the link to my Github page below.  Overall, I scrape about XXX total webpages and establish a dataset of nearly 56,000 fights across XXX individual fighters. 

Note 10/29/25: Stopping for now. Next steps include talking about the total list of fighters, why soem fighters are not included (too obscure for a wikipedia page), defining the rating system including the traditional elo model, the decay function, and bonuses for fight finishes and championship fights/wins (refer to the elo notebook for reference). Then need to decide on final tables and visuals. Also mention that eventually I plan to create more results such as current and all-time elo ratings my division and gender.

</div>
