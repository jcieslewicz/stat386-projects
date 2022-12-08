---
layout: post
title:  "How Much Does Spotify Know About Me?"
date:   2022-12-05
author: Joseph Cieslewicz
description: When analyzed creatively, data from your spotify library can tell very personal stories.
image: /assets/images/SpotifyUser.JPG
---

## Introduction and Overview

“Where words fail, music speaks.” – Hans Christian Anderson

The purpose of this project has been, in a broad sense, to see what my Spotify music library says about me. I started by setting up an application with the Spotify Open API and looping through my entire Spotify library (about 700 songs). Then, I grabbed Spotify’s audio feature data for each of these tracks and compiled it into a cleaned dataset. As a reminder, if you want to learn more about the fields in this data or how to use the API, you can access the documentation [here](https://developer.spotify.com/documentation/web-api/reference/#/). This was all made easier by the Spotipy wrapper package (documentation found [here](https://spotipy.readthedocs.io/en/master/).

Once I had my dataset, I explored the distributions of my variables and the relationships among them. I discovered, for example, that there seem to be strong positive associations among energy, danceability, and loudness, while all three of these have fairly strong negative relationships with acousticness.  My explorations of the dataset led me to a more interesting question: how have my tastes in music changed over time, and what does this say about me? I used seaborn’s locally estimated scatterplot smoothing (LOWESS) tool to track how a few key track features have changed over time. Because I didn’t have event data that would link songs to dates, I had to be creative and create a proxy variable based on how recently the song was added to my library (rank-order). This means that I’m tracking the average features of the songs I liked on Spotify over time. You can see the results below:

![Data Story](https://github.com/jcieslewicz/stat386-projects/raw/main/assets/images/SpotifyLibraryGraphic.jpg)

When we look at the trends in various audio features over time, we can see a cohesive story. Over the course of the first 200 song additions to my library, my music became gradually more acoustic and less danceable, energetic, and valent (happy). When I looked at the specific songs from this period, I was able to connect it to the aftermath of a back injury, when I was not feeling particularly energetic or valent. For the next 200 or so songs, acousticness came back down, popularity began to slowly increase, valence recovered quickly, and energy and danceability followed. Based on the songs in this group, I connect this period to the time immediately following my two-year missionary service. My positivity increased and my music gradually became a little more mainstream, but it took more time for my musical tastes to become as lively as they originally were. This could be attributed to the unique musical libraries that we listen to as missionaries, which few would describe as energetic or danceable. In the most recent 300 or so songs, my songs have become increasingly more popular, while acousticness, valence, danceability, and energy have reached an equilibrium near the midpoint. This suggests to me that my musical tastes have become slightly more in line with popular trends while overall becoming more balanced between happy and sad and energetic and smooth. I’ve included some sample songs below the line graph to provide examples of my music from different time periods.

## Takeaways

The concordance between the features of my Spotify songs and overall trends in my personal life show one example of how seemingly innocuous data can provide insights into surprisingly private details of people’s everyday lives. If I had more specific event data, I could track my mood at different times of day, different seasons of the year, or even in different people’s company. While this data could have uses for good in areas like mental health treatment, it also brings with it considerable privacy considerations. This should serve as yet another example of why as a data scientist you should always think about the people behind your data, and how your work could affect them in real life.

The data and code related to the project can be found in my repo [here](https://github.com/jcieslewicz/SpotifyProject). Don’t forget to comment or ask questions!
