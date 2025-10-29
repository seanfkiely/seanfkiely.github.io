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
  overlay_image: /images/fg_kicks_final.png  # optional: banner image
  overlay_filter: 0.4
  caption: "NFL field goal attempt distribution, 1999–2025"
---

This project visualizes how field goal distances and make percentages have changed over time.
<insert figure or link to image>

## Background

In recent years, there's been much discussion about how the kicking game in the NFL has changed, with today's kickers becoming more accurate and hitting longer field goals than ever before. 
Before the 2025 season, the NFL introduced a rule change allowing teams extended time to prepare, or break in, balls before games. 
Many around the league, such as [Eagles defensive coordinator Vic Fangio](https://www.nbcsports.com/nfl/profootballtalk/rumor-mill/news/vic-fangio-new-kicking-ball-procedures-have-drastically-changed-field-goals) (*Go Birds*), speculated that this so-called ``K-ball'' rule would further increase field-goal accuracy and attempts from greater distances.

This prompted me to explore the history of NFL kicking rules and the evolution of the kicking game. Prior to the 1999 season, team equipment staff would manipulate footballs in all sorts of ways to break them in- allegedly including placing them in dryers, saunas, and microwaves. There's even speculation that some balls were partially filled with helium.

Starting in 1999, the first ``K-ball'', or kicking ball, rule took effect: only officials were allowed to prepare the footballs used for kicking. Special teams players, particularly kickers and punters claimed that the balls were too hard and slick, making holds and kicks more difficult.
The issue came to a head at the end of the 2006 season when Dallas Cowboys quarterback Tony Romo fumbled a snap as the holder on a potential game-winning field goal attempt-resulting in a playoff loss to the Seattle Seahawks.

Following this Cowboys playoff meltdown (not their [last](data:image/jpeg;base64,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)), the league adapted a new rule for the 2007 season, giving teams 60 minutes of access to kicking balls before games to break in three balls using only two towels and a Wilson-branded brush. This offseason, the league extended that time further, allowing equipment staff to prepare balls in line with kicker preferences—similar to other game balls.

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
- **Kickers have evolved.** From 2016–2024, kickers were 15–31 % more likely to make a 50+ yard kick than in 1999–2006, and 7–18 % more likely than in 2007–2015.  
  In 2025 so far, kicks from 55–59 yards are 6 % more likely to be made than in 2016–2024, and 60–64 yard kicks are being made at a whopping 71 %.  
  While kickers are currently at 33% accuracy from 65-69 yards this only includes one made field goal by Chase McLaughlin of the Tampa Bay Buccaneers.
  It’s probably only a matter of time before Justin Tucker’s 66-yard record from the 2021 season is broken. Odds seem to currently be in the favor of Dallas' Brandon Aubrey. Too bad he can't play defense too. 
  

{% include figure image_path="/images/fg_kicks_final.png" caption="Height = Share of FG Attempts in Era; Color = Make %" width="100%" %}



