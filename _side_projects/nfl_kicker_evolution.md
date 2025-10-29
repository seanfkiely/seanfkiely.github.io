---
title: "Evolution of the NFL Kicking Game"
collection: side_projects
date: 2025-10-28
excerpt: "A data visualization of how NFL kicker accuracy and attempt ranges have evolved over time."
tags:
  - Data Visualization
  - Sports Analytics
  - Python
  - NFL
header:
  overlay_image: /images/fg_kicks_final.png
  overlay_filter: 0.4
  caption: "NFL field goal attempt distribution, 1999–2025"
---

This project visualizes how NFL field goal distances and make percentages have changed over time.

## Background

In recent years, there's been much discussion about how the kicking game in the NFL has changed, with today's kickers becoming more accurate and hitting longer field goals than ever before. 
Before the 2025 season, the NFL introduced a rule change allowing teams extended time to prepare, or break in, balls before games. 
Many around the league, such as [Eagles defensive coordinator Vic Fangio](https://www.nbcsports.com/nfl/profootballtalk/rumor-mill/news/vic-fangio-new-kicking-ball-procedures-have-drastically-changed-field-goals) (*Go Birds*), speculated that this so-called ''K-ball'' rule would further increase field-goal accuracy and attempts from greater distances.

This prompted me to explore the history of NFL kicking rules and the evolution of the kicking game. Prior to the 1999 season, team equipment staff would manipulate footballs in all sorts of ways to break them in, allegedly including placing them in dryers, saunas, and microwaves. There's even speculation that some balls were partially filled with helium.

Starting in 1999, the first ''K-ball'', or kicking ball, rule took effect: only officials were allowed to prepare the footballs used for kicking. Special teams players, particularly kickers and punters, claimed that the balls were too hard and slick, making holds and kicks more difficult.
The issue came to a head at the end of the 2006 season when Dallas Cowboys quarterback Tony Romo fumbled a snap as the holder on a potential game-winning field goal attempt, resulting in a playoff loss to the Seattle Seahawks.

Following this Cowboys playoff meltdown (not their last), the league adapted a new rule for the 2007 season, giving teams 60 minutes of access to kicking balls before games to break in three balls using only two towels and a Wilson-branded brush. This offseason, the league extended that time further, allowing equipment staff to prepare balls in line with kicker preferences—similar to other game balls.

## Data 

I use NFL play-by-play data from **`read_nfl_py`** in Python to visualize how “K-ball” rules, kicker quality, and analytics have changed the NFL kicking game.  
The dataset includes every field-goal attempt from 1999 to 2025, divided into four eras:

1. **1999–2006** – Officials handled all preparation of kicking balls.  
2. **2007–2015** – Teams allowed 60 minutes of pre-game access.  
3. **2016–2024** – Same rule, but assessing improvements in kicker talent and strategy over time.  
4. **2025+** – Extended preparation period (“modern” K-ball rule).

The code for producing the figure is available here. The results below cover data through **Week 8 of the 2025 season**, representing roughly 500 field-goal attempts. Running the code later will update the “extended preparation” era automatically, and I plan to refresh this figure at season’s end.

## Analysis

The plot below breaks down field-goal attempts and kicker accuracy across these four periods.  
The **x-axis** shows 5-yard bins of field-goal distance (15 to 70+ yards), and the **y-axis** shows the percentage of total field goals attempted.  
Bar height corresponds to the share of attempts from that binned distance; bar color reflects accuracy (darker = higher make rate).  
Percentages above each bar display the make percentage for that bin.

As analytics-driven “go-for-it” play-calling has increased, especially near the end zone, and kickers seem to be hitting kicks from tremendous distances, we might expect a rightward shift in the distribution of field-goal attempts.  
That’s exactly what the data show.

- **Shorter kicks are less frequent.** Between the 1999–2006 and 2016–2024 eras, there's been a marked decrease in field-goal attempts under 25 yards.  
- **Longer kicks are far more common.** In the initial K-ball era, only ≈9 % of attempts were from 50+ yards, compared to nearly 20 % between 2016 and 2024 and almost 30 % so far in 2025.  
- **Accuracy has improved everywhere.** After allowing teams one hour of preparation instead of official-only handling, make rates increased across every distance bin.  
- **Kickers have evolved.** From 2016–2024, kickers were 15–31% more likely to make a 50+ yard kick than in 1999–2006, and 7–18 % more likely than in 2007–2015.  
  In 2025 so far, kicks from 55–59 yards are 6% more likely to be made than in 2016–2024, and 60–64 yard kicks are being made at a whopping 71%.  
  While kickers are currently at 33% accuracy from 65-69 yards this only includes one made field goal by Chase McLaughlin of the Tampa Bay Buccaneers.
  It’s probably only a matter of time before Justin Tucker’s 66-yard record from the 2021 season is broken. Odds seem to currently be in favor of Dallas' Brandon Aubrey. Too bad he can't play defense too. 
  
<figure style="text-align:center; margin: 2em 0;">
  <img src="/images/fg_kicks_final.png" alt="NFL field goal evolution" style="width:90%; max-width:800px; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.15);">
  <figcaption style="font-style:italic; font-size:0.9em; margin-top:0.5em;">
    <strong>Height = Share of FG Attempts in Era; Color = Make %</strong>
  </figcaption>
</figure>




