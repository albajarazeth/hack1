# YouTube Comments Extractor

A comprehensive system for extracting comments from YouTube videos and generating embeddings with automatic deduplication and clustering. The system includes both Java-based comment extraction and Python-based embedding generation.

## Features

- **Direct YouTube video ID input**: Provide YouTube video IDs directly
- **Multi-threaded comment extraction**: Fetches comments in parallel for efficiency
- **API key management**: Rotates through multiple API keys with rate limiting
- **Comment cleaning**: Removes URLs and normalizes comment text
- **Reply inclusion**: Fetches both top-level comments and their replies
- **Caching**: Caches video information to minimize API calls
- **Embedding generation**: Creates semantic embeddings for comments using transformer models
- **Automatic deduplication**: Removes similar comments using cosine similarity
- **K-means clustering**: Groups similar comments using optimal cluster selection with silhouette analysis
- **Distance-to-centroid computation**: Calculates distance from each comment to its cluster centroid
- **Detailed progress logging**: Shows detailed progress during embedding, deduplication, and clustering
- **Clustering summary JSON files**: Saves detailed clustering results as JSON files in the embed folder
- **Google Colab integration**: Monitors Google Drive for new comment files and processes them automatically
- **Sentiment classification**: Automatically classifies emotions in comments using the GoEmotions model with 27-class emotion detection

## Prerequisites

- Java 8 or higher
- Maven
- Python 3.7+
- YouTube Data API v3 keys

## Setup

1. Clone the repository
2. Add your YouTube API keys to the `.env` file
3. Add your YouTube video IDs to `videoList.txt` (one per line)

## Configuration

### API Keys
Add your YouTube Data API v3 keys to the `.env` file:
```
YOUTUBE_API_KEY_1=your_actual_api_key_here
YOUTUBE_API_KEY_2=your_actual_api_key_here
YOUTUBE_API_KEY_3=your_actual_api_key_here
YOUTUBE_API_KEY_4=your_actual_api_key_here
# Add more keys as needed (up to YOUTUBE_API_KEY_20)
```

### Auto-Embedding Configuration
The auto-embedding script uses the following environment variables:
```
COMMENTS_DIR=/content/drive/My Drive/youtubeComments
EMBEDDINGS_DIR=/content/drive/My Drive/youtubeComments/embed
CHECK_INTERVAL=5
```

The system uses multiple API keys to handle rate limiting and quotas effectively. The more keys you provide, the more efficient the extraction process will be.

## Usage

### Extract Comments for a Single Video
```bash
mvn compile
mvn exec:java -Dexec.mainClass="com.lucy.YouTubeCommentsExtractor" -Dexec.args="VIDEO_ID [OUTPUT_FILE]"
```

Example:
```bash
mvn exec:java -Dexec.mainClass="com.lucy.YouTubeCommentsExtractor" -Dexec.args="dQw4w9WgXcQ"
```

### Extract Comments for Multiple Videos
Add video IDs to `videoList.txt` (one per line), then run:
```bash
mvn exec:java -Dexec.mainClass="com.lucy.YouTubeCommentsExtractor" -Dexec.args="videoList.txt"
```

### Generate Embeddings with Auto-Deduplication and Clustering
Run the auto-embedding script in Google Colab to monitor a Google Drive folder and automatically generate embeddings for new comment files:

```bash
python auto_embed_comments_final.py
```

The script will:
- Monitor the specified Google Drive folder for new comment JSON files
- Generate semantic embeddings using the multilingual-e5-small model
- Apply cosine-based deduplication to remove similar comments
- Perform K-means clustering with silhouette-based optimal cluster selection
- Compute distance-to-centroid for each comment
- Save detailed clustering summary as JSON files in the embed folder
- Show detailed progress logging during all processing steps
- Save the processed embeddings to the output directory

### Sentiment Classification with GoEmotions
Run the sentiment classifier to automatically detect emotions in YouTube comments using the GoEmotions model:

```bash
python sentiment_classifier.py
```

The script will:
- Monitor the Google Drive folder for new comment JSON files
- Classify emotions using the AnasAlokla/multilingual_go_emotions model (27-class emotion detection)
- Apply sigmoid-based multi-label classification for accurate emotion prediction
- Save sentiment results as JSON files with emotion labels
- Support dynamic label loading from model configuration
- Process comments in batches for efficient GPU utilization

### Other Utilities

- `ApiKeyTester.java` - Test your YouTube API keys
- `SampleCommentsAggregator.java` - Aggregate sampled comments from all files
- `FindMissingComments.java` - Find videos without comment files
- `TxtCommentsToJsonConverter.java` - Convert text files to JSON format
- `DebugTest.java` - Debug the extraction pipeline

## Output

Comments are saved in the `comments/` directory as JSON files with the naming convention `{VIDEO_ID}_comments.json`. Each file contains an array of comment strings.

Embeddings are saved as compressed `.npz` files with deduplication and clustering applied, containing:
- Embedding vectors
- Comment IDs
- Cluster labels
- Cluster centroids
- Distance-to-centroid values

Clustering summaries are saved as `.json` files in the embed folder with the same base name as the corresponding `.npz` file, containing:
- Input/output file information
- Total and deduplicated comment counts
- Embedding dimension
- Number of clusters and silhouette score
- Cluster distribution
- Processing timestamp
- Clustering algorithm used

## Project Structure

```
src/
├── main/java/com/lucy/
│   ├── ApiKeyManager.java          # Manages YouTube API keys with rate limiting
│   ├── ApiKeyTester.java           # Tests YouTube API keys
│   ├── DebugTest.java              # Debugging utility
│   ├── FindMissingComments.java    # Finds missing comment files
│   ├── SampleCommentsAggregator.java # Aggregates sampled comments
│   ├── TxtCommentsToJsonConverter.java # Converts text to JSON
│   ├── VideoCache.java             # Caches video search results
│   ├── YouTubeCommentsExtractor.java # Main extractor for YouTube videos
│   ├── YouTubeSearchHelper.java    # YouTube video information helper
│   └── YoutubeCommentScraper.java  # Core comment extraction
auto_embed_comments_final.py        # Auto-embedding with deduplication and clustering for Google Colab
sentiment_classifier.py             # Auto-sentiment classification using GoEmotions model
README.md                         # This file
```

## Files

- `videoList.txt` - Input file with YouTube video IDs (one per line)
- `comments/` - Output directory for extracted comments
- `.env` - Environment file for API keys (not committed to git)
- `auto_embed_comments_final.py` - Auto-embedding script with deduplication and clustering for Google Colab
- `sentiment_classifier.py` - Auto-sentiment classification script using GoEmotions model for emotion detection