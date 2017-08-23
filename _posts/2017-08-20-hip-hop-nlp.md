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

I realize that's not easy to read so I zoomed in to the top 20 and bottom 20 for readability. See where your favorite artist scored!

<figure>
  <a href="/assets/images/Hip Hop/top20.png">
  <img src="/assets/images/Hip Hop/top20.png"></a>
</figure>

<figure>
  <a href="/assets/images/Hip Hop/bottom20.png">
  <img src="/assets/images/Hip Hop/bottom20.png"></a>
</figure>

### Topic Modeling

Now that we have a good idea of the complexity of each artist's lyrics, let's dig deeper into what they're actually talking about. **Are Artists really sending less of a message than they used to?**

To do this, I used Term Frequency-Inverse Document Frequency (TF-IDF) to extract the importances of the words (I included bi-grams too). Then I used Non-Negative Matrix Factorization (NMF) to take this matrix of word importances and group words that appear frequently together to extract topics.

I had to play around with the minimum and maximum document frequency parameters a bit, but was able to find a group of 4 to 5 topics that represented the majority of topics being discussed. These topics can certainly be broken down to find sub-topics, but for this exploration, the high level topics were good enough.

Here's the raw output of the NMF in a table:

<figure>
  <a href="/assets/images/Hip Hop/topics.png">
  <img src="/assets/images/Hip Hop/topics.png"></a>
</figure>

This output made sense, it looks like it's grouping the right words together. Based on the output I gave each topic a proper name:

- Topic 1 -> **Thoughts/Brotherhood:** songs that make commentary and send a message will likely fall into this bucket.
- Topic 2 --> **Love/Feelings:** ...will probably find a lot of Drake in there.
- Topic 3 --> **Sexual:** a lot of overlap with previous topic but definitely less feelings in here.
- Topic 4 --> **Gangs/Money:** find a lot of songs talking about drugs, getting money, etc.
- Topic 5 --> **...Ice Cube?:** I guess he gets his own topic? There are like 300 songs from him and his various groups like N.W.A. I should probably filter this out or just group it with the gangs topic but I'll keep it for fun.

We can look into a few songs that most people will know the lyrics too and see how accurate the topic models are:

<figure>
  <a href="/assets/images/Hip Hop/topic_examples.png">
  <img src="/assets/images/Hip Hop/topic_examples.png"></a>
</figure>

Awesome! Drake is definitely talking about a girl the whole time in "Child's Play", "Ten Crack Commandments" is all about drugs, and Kendrick is talking about his rough up-bringing in "DNA." The topic model is working!

And... Ice Cube talks about Ice Cube stuff...

<figure>
  <a href="/assets/images/Hip Hop/ice_cube.png">
  <img src="/assets/images/Hip Hop/ice_cube.png"></a>
</figure>

### Topic Trends

Now we can see how these topics change over time. I'll average up the topic scores for each decade and we can see if we notice any changes:

<figure>
  <a href="/assets/images/Hip Hop/topic_trends.png">
  <img src="/assets/images/Hip Hop/topic_trends.png"></a>
</figure>

Interesting! We see see that people are talking about sex more than they used to, and the thoughts/brotherhood topic is clearly on the decline! This supports our hypothesis that artists are making less social commentary these days!

### Conclusions

To answer our initial questions:

- Do artists actually have less lyrics now?
  - **Yes, a slight decline**
- Do artists use less variety in their words?
  - **Yes, there is a sharp decline since 1990s**
- Are they sending less of a message than they used to:
  - Talking less about ideas/social commentary
    - **Yes, topics like this are clearly declining**
  - More emphasis on partying, girls, cars, etc.
    - **Definitely an increase in Sex and Love, but actually a decline in gangs/money related topics**


Check out my [github repo](https://github.com/maielld1/Hip-Hop-Lyrics-NLP-Analysis) for code
