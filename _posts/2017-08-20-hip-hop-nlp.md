---
layout: single
title: "Hip Hop Music Under the NLP Scope"
header:
  image: /assets/images/Hip Hop/crowd.jpg
  caption: "Photo credit: [**klejonka**](https://www.klejonka.info/2017kimage-kendrick-lamar-tde-wallpaper.awp)"
permalink: /posts/2017/08/Hip_Hop_NLP
---

Hip Hop music is actually the most listened to genre in the world, [according to an analysis of 20 billion songs on Spotify.](http://www.independent.co.uk/arts-entertainment/music/news/hip-hop-is-the-most-listened-to-genre-in-the-world-according-to-spotify-analysis-of-20-billion-10388091.html)

But some people say Hip Hop is Dead:

> "Everybody sound the same, 
commercialize the game
Reminiscing when it wasn't all business
It forgot where it started"

-Nas "Hip Hop is Dead" 2006

But what does he mean by this? He's most likely referring to some changes that have been gradually occurring in the genre.

Autotune became cool for a while, every track has like 5 featured artists now, and the rise in popularity has made the genre into more of a money making business than an opportunity to make interesting social commentary through music.  

And this was in 2006. I wonder what Nas has to say now with all of the "mumble rappers" on the rise...

### Investigation:

After learning more in depth about Natural Language Processing and Unsupervised Learning techniques at Metis, I thought this would be a great opportunity to take an objective look at Rap and Hip Hop Lyrics to see if the math supports Nas's claim.

I'll look to answer a few questions in my analysis:

- Do artists actually have less lyrics now?
- Do artists use less variety in their words?
- Are they sending less of a message than they used to:
  - Talking less about ideas/social commentary
  - More emphasis on partying, girls, cars, etc.

Maybe we'll be able to answer some other questions along the way.

### The Data:

1. Scraped Lyrics from LyricWikia
  - Ended up with about 70 popular artists between 1970 and 2017
  - 8000+ tracks
2. Spotify Metrics using their API (Useful for Recommender)
  - metrics on danceability, energy, tempo, etc.
  - Found 4000+ of those songs
  - No Jay-Z data because he's only on Tidal ... sigh
3. Store in MongoDB

### Initial Analysis and Exploration:

After gathering my music data and preprocessing the lyrics (tokenizing, removing stop words, etc.), I threw it all into wordcloud to get an idea of how the words changed over time.

<figure class="half">
  <a href="/assets/images/Hip Hop/lastpoets.png">
  <img src="/assets/images/Hip Hop/lastpoets.png"></a>
  <a href="/assets/images/Hip Hop/cloud80.png">
  <img src="/assets/images/Hip Hop/cloud80.png"></a>
</figure>

So disclaimer here, **I only have lyrics from one artist in 1970, The Last Poets.** But it's still interesting to see as they clearly talked a lot about ideas and making social commentary with words like "black" and "revolution" coming up as the most frequent. The types of words become less frequent in the 80s. Then we move on to the 90s and 2000s and see a shift to a lot of profanity ("sword" is really "s-word" for "shit" and so on for other tokens ending in "word").

<figure class="third">
  <a href="/assets/images/Hip Hop/cloud1990.png">
  <img src="/assets/images/Hip Hop/cloud1990.png"></a>
  <a href="/assets/images/Hip Hop/cloud2000.png">
  <img src="/assets/images/Hip Hop/cloud2000.png"></a>
  <a href="/assets/images/Hip Hop/cloud10.png">
  <img src="/assets/images/Hip Hop/cloud10.png"></a>
</figure>

#### Lyrical Complexity

To measure the lyrical complexity of a song, I created a simple metric to essentially get the ratio of unique words in the song to the total words. Here's an example of how this was calculated using a line from an extremely lyrically complex song:

> "Panda, Panda
  Panda, Panda, Panda, Panda"
  -Desiigner "Panda" (2006)

- Total Words: 6
- Unique Words: 1
- Complexity = (Unique Words / Total Words) x Unique Words
             = 0.1667

Ok...maybe a bad example. But you get the point. A song like this will score very low for this metric.

So I applied this calculation on every song in my database and found some interesting results.

<figure class="half">
  <a href="/assets/images/Hip Hop/songlength.png">
  <img src="/assets/images/Hip Hop/songlength.png"></a>
  <a href="/assets/images/Hip Hop/complex_time.png">
  <img src="/assets/images/Hip Hop/complex_time.png"></a>
  <figcaption>Decrease in lyric length and complexity over time</figcaption>
</figure>

There is a clear decreasing trend for simply the length of the lyrics, but we notice a sharper decline when applying our lyrical complexity metric. It's been decreasing more dramatically even after Nas's "Hip Hop is Dead" release.

Let's take a look at where each artist ranks in lyrical complexity.

<figure>
  <a href="/assets/images/Hip Hop/artists_by_complexity.png">
  <img src="/assets/images/Hip Hop/artists_by_complexity.png"></a>
</figure>

There's Desiigner! All the way at the bottom, so my metric must me valuable! We also see Wu Tang Clan and Nas at the top, which makes sense!
