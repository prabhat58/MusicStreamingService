
# Top 100 Trending Songs on a Music Streaming Service

## Overview
This project implements a **Top 100 Trending Songs** feature for a Music Streaming Service. The top 100 songs are dynamically calculated based on various factors such as play count, user rating, social media shares, geographic popularity, and recency. The calculations are optimized to ensure quick retrievals and minimize database load. The trending score is pre-calculated in the **write path** during song streaming, which helps improve performance in the **read path**.

## Database of Choice
**SQL Database** has been chosen to store the data for this project. In a music streaming platform, there are many entities such as Artist, Song, Album, Playlist, User, and Track, along with relationships between them (e.g., one Artist having multiple albums, one User having multiple playlists, etc.). A relational database helps manage these entities and relationships efficiently.

## Data Source Model

The `Song` class is the primary model for this project. Below is the class definition:

```csharp
public class Song
{
    public string SongId { get; set; }
    public string Title { get; set; }
    public string Artist { get; set; }
    public string Album { get; set; }
    public string Genre { get; set; }
    public int PlayCount { get; set; }
    public double UserRating { get; set; }
    public int SocialMediaShares { get; set; }
    public double GeographicPopularity { get; set; }
    public DateTime LastPlayed { get; set; }
    public double TrendingScore { get; set; }
}
```

**Note**:  
The `Song` class will contain the mentioned properties, and every time a song is streamed, the **TrendingScore** will be calculated and updated (in the write path). This approach helps improve the read path when querying and retrieving the top 100 songs.  
Since the write path is not covered as part of this assignment, the **TrendingScore** is calculated after reading the static data, which is used to showcase how the **TrendingScore** calculation algorithm works.


## TrendingScore Calculation Algorithm

The **TrendingScore** is calculated based on multiple factors that determine the popularity of a song. The algorithm normalizes these factors, applies a decay function for recency, and adjusts the weights of each factor to calculate the final score. Below are the factors considered in the calculation:

### Factors Considered

1. **Play Count**: Number of times the song has been played.
2. **User Rating**: Average rating given by users.
3. **Social Media Shares**: Number of times the song has been shared on social media.
4. **Geographic Popularity**: Popularity of the song in different regions.
5. **Recency**: Time elapsed since the song was last played.

### Calculation Details

1. **Normalization**: Each factor is normalized to a value between 0 and 1 to ensure no single factor disproportionately influences the final score.
2. **Decay Function for Recency**: A decay function is applied to give more weight to recent plays of a song.
3. **Weight Adjustments**: The weights for each factor are adjusted based on their importance:
   - **Play Count Weight**: 0.4
   - **Recency Weight**: 0.3
   - **User Rating Weight**: 0.1
   - **Social Media Shares Weight**: 0.1
   - **Geographic Popularity Weight**: 0.1

## Performance and Handling Large Data

### Approach

When the client makes a request for the first time, the top 100 songs are fetched from the database. To improve performance on subsequent calls, the data is cached with a **TTL (Time-To-Live)** of 24 hours, allowing subsequent requests to retrieve the data from the cache instead of making additional network calls to the database. This reduces the load on the database and improves response times.

### First-Time Query Bottleneck

While caching improves performance for repeated requests, the first request can still be a bottleneck. To solve this issue, the **TrendingScore** is pre-calculated when the song is streamed in the **write path**. By calculating and updating the **TrendingScore** during the streaming process, the records are filtered out at the database level itself. This minimizes the amount of data transferred and processed in memory, which is important when dealing with large amounts of data (millions of records).

## Prerequisites

1. **.NET 8 SDK**: Ensure you have the .NET 8 SDK installed. You can download it from the [official .NET download page](https://dotnet.microsoft.com/download).

## Steps to Run the Project Locally

1. Unzip the project and navigate to the `Music-Streaming-Service\Music-Streaming-Service` directory.
2. Open the `Music-Streaming-Service.sln` file.
3. Restore the project dependencies by running the following command:
   ```bash
   dotnet restore
4. Run the project using the following command:
    ```bash
   dotnet run
5. Open your web browser and navigate to the following URL:

[https://localhost:44360/swagger/index.html](https://localhost:44360/swagger/index.html)

## Unit Tests

- This solution also contains unit tests, which can be found at the path: `Music-Streaming-Service\MusicStreamingService.Tests`
- If you are using Visual Studio 2019 or later, you can click on **Tests** > **Run All Tests** to execute these tests.


