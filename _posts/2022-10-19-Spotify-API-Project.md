---
layout: post
title:  "Spotify API: A brief primer for data scientists"
date:   2022-10-19
author: Joseph Cieslewicz
description: Use the Spotipy library to pull data about your favorite music from Spotify.
image: /assets/images/Spotify_Developers_Logo.png
---

## Introduction

As a music enthusiast, I love Spotify’s recommender system. I can pick a song, genre, or artist that I like, and usually the recommender does a pretty good job of coming up with new songs that I like. However, as a data scientist, I’d like to get more insight into how this recommendation system works. More generally, I’d like to have more data about what I’m listening to. Fortunately, this is easily possible with Spotify’s open API. 

## Spotify API: Getting Started

Spotify has an amazing open API, that can be used for noncommercial and commercial purposes. You can read the full terms of service [here](https://developer.spotify.com/terms/) and the full documentation [here](https://developer.spotify.com/documentation/web-api/reference/#/).  

To get access to the Spotify API, you just need a regular Spotify account. Then, log in [here](https://developer.spotify.com/dashboard/). You’ll need to register a new app, which isn’t as scary as it sounds (it’s all point and click). Click on this app from the dashboard to find three important pieces of information that you should save down. The Client ID and Client Secret are your API keys, found on the main page of the application. 

![Dashboard Screenshot](/assets/images/App_Dashboard_Screenshot.jpg)

Then, you’ll need a redirect URI. Go to Edit Settings -> Redirect URI, and add any valid URL. I recommend something local (unless you’re deploying a web app), like http://localhost:8080.  

![Settings Screenshot](/assets/images/App_Settings_Screenshot.jpg)

The last piece of information you’ll need is your Spotify username, which is accessible on the front page of your Spotify profile, whether online or in the app. I recommend saving these 4 pieces of information into a separate accessible python class, but you can decide the best way to secure and access them.

## Spotipy: Connecting to the API

Like many popular open APIs, the Spotify API has an accompanying python wrapper package, Spotipy. Read the full documentation [here](https://spotipy.readthedocs.io/en/master/). This package is great because it has wrapper functions for just about every endpoint in the API.  

![Python Logo](/assets/images/Spotify_Python.jpg)

There are two ways to establish a connection to the API through Spotipy: Authorization Code and Client Credentials. As I understand it, the Authorization Code Flow is used for when you need to access user-specific information, while the Client Credentials Flow is used for server-to-server interactions. We’ll be using Authorization Code to access information from my personal library, which means we’ll also need to choose an appropriate scope (see the complete guide to scopes [here](https://developer.spotify.com/documentation/general/guides/authorization/scopes/).  

Spotipy provides a built-in class called Spotify OAuth for Authorization Code access. Here’s an example of how I used it:

```
from spotipy.oauth2 import SpotifyOAuth

# This is my class where I've stored private access information
my_tokens = tokens()

CLIENT_ID = my_tokens.CLIENT_ID
CLIENT_SECRET = my_tokens.CLIENT_SECRET
username = my_tokens.username
scope_top_tracks = 'user-top-read'
scope_saved_tracks = 'user-library-read'

sp2 = spotipy.Spotify(auth_manager=SpotifyOAuth(scope=scope_saved_tracks, client_id = CLIENT_ID, client_secret = CLIENT_SECRET, redirect_uri='http://localhost:8080', username=username))
```

Now, to get my saved tracks, I’ll use the current_user_saved_tracks wrapper. This endpoint can only retrieve 50 tracks at a time, so I’ll have it loop until it’s retrieved my entire library (about 700 songs): 

```
offset = 0
limit = 50

names = []
ids = []
release_dates = []
popularity = []
uris = []

# I need to use the spotipy object with the library scope:
my_tracks = sp2.current_user_saved_tracks(limit = limit, offset = offset)

while(len(my_tracks['items']) > 0):
    for i in range(50):
        if len(my_tracks['items']) >= i+1:
            try: 
                current_track = my_tracks['items'][i]['track']

                uris.append(current_track['uri'])
                names.append(current_track['name'])
                ids.append(current_track['id'])
                release_dates.append(current_track['album']['release_date'])
                popularity.append(current_track['popularity'])  
            except:
                print(i)
                  
    
    offset = offset + 50
    my_tracks = sp2.current_user_saved_tracks(limit = limit, offset = offset)
```

Then, for every URI I’ve pulled, I’ll get the audio features. You can read more about Spotify’s audio features data [here](https://developer.spotify.com/discover/). 

```
track_features = []
for uri in uris:
    track_features.append(sp2.audio_features(uri))
```

From here, it’s a few simple data wrangling steps until we get a complete dataset of audio features for all 703 songs in my library. For a jupyter notebook with the complete code, see my repository [here](https://github.com/jcieslewicz/SpotifyProject).

## Conclusion:
This is an example of how simple it can be to access data from an open API, especially using python wrapper libraries. Whenever at all possible, it’s better for both ethical and practical reasons to use an API instead of scraping data. For example, in this case, it would be literally impossible to find a stable webpage that contains all of this data for scraping. But with the API, I can pull it all in a neat format in just a few lines of code. In my next post, I’ll dig into this data and see what we can learn about my music preferences over the years.

