# Music Popularity and Trends Analysis Across Spotify and YouTube

## Project Overview

This project aims to analyze and compare the popularity and trends of music tracks across Spotify and YouTube, identifying patterns, correlations, and insights. By leveraging multiple free-to-use APIs, we will gather data on music tracks and videos for various artists. The analysis will be performed using Python libraries such as `pandas`, `numpy`, and `matplotlib`.

## Objectives

- Fetch data on popular music tracks using the Spotify API.
- Obtain data on music videos for the same tracks using the YouTube Data API.
- Perform data cleaning and preparation to handle missing values and merge datasets.
- Conduct exploratory data analysis (EDA) to identify trends and correlations.
- Visualize the data using various plotting techniques.
- Summarize key findings and provide actionable insights.

## APIs Used

1. **Spotify API**: For fetching data on music tracks, including features like popularity, danceability, energy, and more.
2. **YouTube Data API**: For obtaining data on music videos, including view counts, likes, comments, and more.

## Libraries Used

- `pandas`
- `numpy`
- `matplotlib`
- `requests`
- `spotipy`
- `google-api-python-client`

## Steps to Implement

1. **Set Up Environment**:
   - Install necessary libraries: `pandas`, `numpy`, `matplotlib`, `requests`, `spotipy`, `google-api-python-client`.
   - Obtain API keys for Spotify and YouTube.

2. **Fetch Data**:
   - Use the Spotify API to fetch data on popular music tracks, including features like popularity, danceability, energy, and more.
   - Use the YouTube Data API to get data on music videos for the same tracks, including view counts, likes, comments, and more.

3. **Data Cleaning and Preparation**:
   - Clean the fetched data to handle missing values, incorrect data types, and duplicates.
   - Merge the Spotify and YouTube data based on track names or other identifiers.

4. **Exploratory Data Analysis (EDA)**:
   - **Descriptive Statistics**: Calculate mean, median, standard deviation, and other statistics for music features and video metrics.
   - **Correlation Analysis**: Analyze the correlation between Spotify features (e.g., popularity, danceability) and YouTube metrics (e.g., view counts, likes).
   - **Visualization**:
     - **Scatter Plots**: Plot Spotify popularity vs. YouTube views, danceability vs. likes, etc.
     - **Heatmaps**: Create heatmaps to visualize the correlation matrix.
     - **Time Series Analysis**: If historical data is available, analyze trends over time.

5. **Insights and Conclusions**:
   - Summarize the key findings from the EDA.
   - Identify any significant trends or correlations.
   - Provide actionable insights or recommendations based on the analysis.

6. **Documentation and Presentation**:
   - Document the entire process, including code, visualizations, and findings.
   - Create a presentation or report to showcase the results.
  
## Conclusion
This project provides a comprehensive analysis of music popularity and trends across Spotify and YouTube. By leveraging multiple APIs and performing EDA, we can gain valuable insights into the relationship between music features and video metrics. The visualizations and findings from this project can be used to inform marketing strategies, understand audience preferences, and drive further research.

## Example Code Snippet

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import requests
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
from googleapiclient.discovery import build

# Spotify API credentials
spotify_client_id = 'your_spotify_client_id'
spotify_client_secret = 'your_spotify_client_secret'
spotify_auth_manager = SpotifyClientCredentials(client_id=spotify_client_id, client_secret=spotify_client_secret)
spotify = spotipy.Spotify(auth_manager=spotify_auth_manager)

# YouTube API credentials
youtube_api_key = 'your_youtube_api_key'
youtube = build('youtube', 'v3', developerKey=youtube_api_key)

# Fetch Spotify data
spotify_tracks = spotify.playlist_tracks('your_playlist_id')
spotify_data = []
for item in spotify_tracks['items']:
    track = item['track']
    spotify_data.append({
        'track_name': track['name'],
        'artist': track['artists'][0]['name'],
        'popularity': track['popularity'],
        'danceability': track['danceability'],
        'energy': track['energy']
    })
spotify_df = pd.DataFrame(spotify_data)

# Fetch YouTube data
youtube_data = []
for track in spotify_data:
    search_response = youtube.search().list(
        q=f"{track['track_name']} {track['artist']}",
        part='snippet',
        maxResults=1
    ).execute()
    video_id = search_response['items'][0]['id']['videoId']
    video_response = youtube.videos().list(
        id=video_id,
        part='statistics'
    ).execute()
    video_stats = video_response['items'][0]['statistics']
    youtube_data.append({
        'track_name': track['track_name'],
        'artist': track['artist'],
        'views': int(video_stats['viewCount']),
        'likes': int(video_stats['likeCount']),
        'comments': int(video_stats['commentCount'])
    })
youtube_df = pd.DataFrame(youtube_data)

# Merge dataframes
merged_df = pd.merge(spotify_df, youtube_df, on=['track_name', 'artist'])

# Plot Spotify popularity vs. YouTube views
plt.scatter(merged_df['popularity'], merged_df['views'])
plt.xlabel('Spotify Popularity')
plt.ylabel('YouTube Views')
plt.title('Spotify Popularity vs. YouTube Views')
plt.show()
