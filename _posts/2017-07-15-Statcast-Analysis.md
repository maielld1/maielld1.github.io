---
layout: single
title: Looking at the Value of Statcast in MLB
header:
  image: /assets/images/Statcast/judge.jpg
  caption: "Photo credit: [**MLB**](https://mlb.com)"
permalink: /posts/2017/07/Statcast-Analysis
---

If you've watched any MLB games or ESPN over the past couple years, you most likely have heard the announcers talking about Statcast or launch angles/velocities. Baseball fans and announcers are having a lot of fun with this relatively new way to analyze the game.

For those who have not heard of Statcast, it is just a tool that uses super powerful high-speed cameras installed in every pro baseball stadium that are capable of measuring aspects of the game that were not possible before. When you hear people talk about launch angle and velocity, they are referring to the angle and speed that the baseball leaves the bat when someone hits the ball. Statcast is essentially really good at measuring this exact moment:

{% raw %}<img src="{{ site.url }}/assets/images/Statcast/cast.jpg" alt="" class="full">{% endraw %}


Statcast captures many more metrics like angle and spin of incoming pitch, range of fielders on each play, and many more. Why is this significant? Is it significant?

### The Big(ly) League Problem

Measuring the value of baseball players is extremely difficult. General Managers of baseball teams struggle to efficiently spend money to field a winning team. They can't afford to pay players who underperform too much money. Ever since the (can't avoid the cliché reference for any baseball stats related topic) Moneyball era, teams are getting better at making data-driven decisions rather than accepting outdated practices in judging talent.

Where can Statcast data fit in this?

**Can we predict a player's true value accurately by looking solely at their Statcast data?**

Let's see how much a player's average launch angle, velocity, and barrels can tell us about them.

### The Approach

When we say player value, in baseball that means WAR, or Wins Above Replacement. If you want to read more about it, or any baseball stats for that matter, go to [FanGraphs](http://www.fangraphs.com/library/war/war-position-players/). If you don't feel like reading about it, it just is an overall measure of how many more wins a player contributes to a baseball team than some replacement player on the bench.

We can create two regression models, one using strictly Statcast data, and another using traditional metrics to compare, and see which has higher predictability (R^2).

Let's look at the data first.

Note: I collected traditional stats (plus some more advanced stats analyzing power and plate discipline) from FanGraphs since 2015. I used [Baseball-Savant](https://baseballsavant.mlb.com/) for collecting Statcast data over the same time period.

### Data Viz / EDA
