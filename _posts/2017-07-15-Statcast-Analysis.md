---
layout: single
title: The Value of Statcast Data in MLB
header:
  image: /assets/images/Statcast/judge.jpg
  caption: "Photo credit: [**MLB**](https://mlb.com)"
permalink: /posts/2017/07/Statcast-Analysis
---

If you've watched any MLB games or ESPN over the past couple years, you most likely have heard the announcers talking about Statcast or launch angles/velocities. Baseball fans and announcers are having a lot of fun with this relatively new way to analyze the game.

For those who have not heard of Statcast, it is just a tool that uses super powerful high-speed cameras installed in every pro baseball stadium that are capable of measuring aspects of the game that were not possible before. When you hear people talk about launch angle and velocity, they are referring to the angle and speed that the baseball leaves the bat when someone hits the ball. Statcast is essentially really good at measuring this exact moment:

{% raw %}<img src="/assets/images/Statcast/cast.jpg" alt="" class="full">{% endraw %}


Statcast captures many more metrics like the angle and spin of an incoming pitch, the range of fielders on each play, and many more. Why is this significant? Is it significant?

### The Big(ly) League Problem

Measuring the value of baseball players is extremely difficult. General Managers of baseball teams struggle to efficiently spend money to field a winning team. They can't afford to pay players who underperform too much money. Ever since the (can't avoid the cliché reference for any baseball stats related topic) Moneyball era, teams are getting better at making data-driven decisions rather than accepting outdated practices in judging talent.

Where can Statcast data fit in this?

**Can we predict a player's true value accurately by looking solely at their Statcast data?**

Let's see how much a player's average launch angle, velocity, and barrels can tell us about them.

### The Approach

When we say player value, in baseball that means WAR, or Wins Above Replacement. If you want to read more about it, or any baseball stats for that matter, go to [FanGraphs](http://www.fangraphs.com/library/war/war-position-players/). If you don't care for the details, it is just an overall measure of how many more wins a player contributes to a baseball team than some replacement player on the bench.

We can create two regression models, one using strictly Statcast data, and another using traditional metrics to compare, and see which has higher predictability (R^2).

Let's look at the data first.

Note: I collected traditional player stats (plus some more advanced stats analyzing power and plate discipline) from FanGraphs since 2015. I used [Baseball-Savant](https://baseballsavant.mlb.com/) for collecting Statcast data over the same time period.

### Data Viz / EDA

Looking at about 90,000 baseballs hit in play over that time period, we can see a clear relationship between likelihood of getting a hit and the angle/speed of the ball off the bat.

<figure>
  <a href="/assets/images/Statcast/heat.png"><img src="/assets/images/Statcast/heat.png"></a>
</figure>

We can also see that you will gain more bases on a hit as you get closer to a sweet spot of angle/velocity.

<figure>
  <a href="/assets/images/Statcast/scatter.png"><img src="/assets/images/Statcast/scatter.png"></a>
</figure>

Now let's compare some stat groups and see how they correlate to WAR.

<figure class="third">
    <a href="/assets/images/Statcast/traditional_corr.png"><img src="/assets/images/Statcast/traditional_corr.png"></a>
    <a href="/assets/images/Statcast/adv_corr.png"><img src="/assets/images/Statcast/adv_corr.png"></a>
    <a href="/assets/images/Statcast/statcast_corr.png"><img src="/assets/images/Statcast/statcast_corr.png"></a>
    <figcaption>Correlations of some traditional, advanced, and statcast metrics to WAR (left to right)</figcaption>
</figure>

Wow! Statcast data does not have a strong correlation with WAR at all compared to the other metrics. This will probably mean it has low predictability.

### Why Are the Correlations So Low for Statcast Data?

Here's great example of why:

{% raw %}<img src="/assets/images/Statcast/judge_altuve.jpg" alt="" class="full">{% endraw %}

On the left is Aaron Judge (featured in the post header). He's 6' 7'' and about 280 lbs. He's a beast. Almost twice as big as the guy to his right, José Altuve, who is 5' 6'' and 165 lbs. No matter how hard Altuve tries, he is not going to hit the ball nearly as hard as Judge. Judge hits the ball with an average exit velocity of 96 MPH and barrels the ball 26.4% of the time while Altuve averages out at 85 MPH with a barrel occurring only 6.1% of the time. You might assume he is not as good at this game that revolves around hitting round things with sticks as hard as possible...

Well...

They are actually both the league leaders (Judge is ahead, but it's close) in WAR! How is that possible!?

There must be a lot more to this game than power at the plate, which when you think about it, is what Statcast is mostly measuring.

<figure class="half">
    <a href="/assets/images/Statcast/war_speed.png"><img src="/assets/images/Statcast/war_speed.png"></a>
    <a href="/assets/images/Statcast/war_angle.png"><img src="/assets/images/Statcast/war_angle.png"></a>
    <figcaption>Weak Correlations with WAR</figcaption>
</figure>

<figure class="half">
    <a href="/assets/images/Statcast/slg_speed.png"><img src="/assets/images/Statcast/slg_speed.png"></a>
    <a href="/assets/images/Statcast/slg_angle.png"><img src="/assets/images/Statcast/slg_angle.png"></a>
    <figcaption>Stronger Correlations with SLG</figcaption>
</figure>

We can prove this with linear models as well.

### Modeling

I went ahead and made linear models combining the different eras of baseball statistics separately. I trained the models on 80% of my scraped data and below are the predicted vs. actual WAR values. The red line just shows y=x which would be indicate completely accurate prediction (1.0 R^2).  

<figure>
  <a href="/assets/images/Statcast/trad_model.png"><img src="/assets/images/Statcast/trad_model.png"></a>
  <figcaption>Traditional Stats (~0.4 R^2)</figcaption>
</figure>

<figure>
  <a href="/assets/images/Statcast/adv_model.png"><img src="/assets/images/Statcast/adv_model.png"></a>
  <figcaption>Advanced Stats (~0.512 R^2)</figcaption>
</figure>

<figure>
  <a href="/assets/images/Statcast/cast_war_model.png"><img src="/assets/images/Statcast/cast_war_model.png"></a>
  <figcaption>Statcast Stats (~0.21 R^2 ... worst model :( ) </figcaption>
</figure>

As we expected from the correlations earlier, Statcast data was not sufficient alone in predicting player WAR. Again, we see much stronger predictive ability for a player's SLG numbers:

<figure>
  <a href="/assets/images/Statcast/slg_model.png"><img src="/assets/images/Statcast/slg_model.png"></a>
  <figcaption>Statcast to SLG Model (~0.5 R^2) </figcaption>
</figure>

### Conclusion and Thoughts

1) Baseball involves a lot of randomness, can’t expect a nearly perfect model.

2) Statcast is interesting, but we (a GM) will also need many more features to measure player value.

3) Stronger measure of a player’s slugging ability, so there is some value to Statcast!

4) Maybe better for player by player analysis rather than trying to generalize over the entire league.

Examples:

As a player gets older, their exit velocity likely falls. Statcast can be used to identify declining players.

On the other side, a coach might notice a player increasing their production at the plate. They look at the numbers and find that their launch angle has increased by 4 degrees since last season. The coach can tell them to keep swinging with that new uppercut! [We have seen this in the MLB already](https://www.washingtonpost.com/graphics/sports/mlb-launch-angles-story/?utm_term=.fa8002e91eff)
