# Image Scraper Project

A Python-based image scraping tool that downloads images from DuckDuckGo search results for Points of Interest (POI) in Bulacan, Philippines.

## Overview

This project scrapes images for various tourist attractions, historical sites, natural landmarks, and cultural heritage locations in Bulacan province. It uses the DuckDuckGo search engine API to find and download images based on location data from a CSV file.

## Project Structure

```
.
├── scraper.ipynb          # Main image scraping notebook
├── notebook.ipynb         # Basic scraping examples and tests
├── diagnosis.ipynb        # Image validation and cleanup utilities
├── poi.csv               # Points of Interest data (name, municipality, coordinates, etc.)
├── downloaded_images/    # Storage for scraped images
├── test_images/          # Storage for test images
└── README.md
```

## Features

- **Automated Image Scraping**: Downloads multiple images per search query using DuckDuckGo
- **CSV-based Input**: Reads POI data from `poi.csv` containing locations in Bulacan
- **Smart Filtering**: Automatically skips WebP images to ensure compatibility
- **Error Handling**: Gracefully handles download failures and timeouts
- **Image Validation**: Includes utilities to detect and remove corrupted images
- **Progress Tracking**: Real-time progress updates during bulk downloads

## Requirements

Install dependencies using:

```sh
pip install requests ddgs pandas Pillow
```

### Key Dependencies

- `requests` - HTTP library for downloading images
- `ddgs` - DuckDuckGo search API wrapper
- `pandas` - Data manipulation for CSV processing
- `Pillow` - Image validation and processing

## Usage

### Basic Image Download

Use the `download_images` function from `scraper.ipynb`:

```python
from ddgs import DDGS
import requests
import os

download_images("Mt. Balagbag in San Jose Del Monte Bulacan", max_results=10)
```

### Batch Processing POI Data

The scraper reads from `poi.csv` and generates search queries automatically:

```python
# Load POI data
df = pd.read_csv('poi.csv')
search_queries = generate_search_queries(df_dict)

# Download images for all POIs
for query in search_queries:
    download_images(query, 10)
```

### Image Validation

Use `diagnosis.ipynb` to check and clean downloaded images:

```python
# Check for broken images
from PIL import Image

broken_images = []
for img_name in os.listdir('downloaded_images/'):
    try:
        img = Image.open(img_path)
        img.verify()
    except (IOError, SyntaxError):
        broken_images.append(img_name)

# Remove broken images
for img_name in broken_images:
    os.remove(os.path.join('downloaded_images/', img_name))
```

## Data Format

The `poi.csv` file contains POI information with the following key columns:

- `name` - Name of the point of interest
- `municipality` - Municipality where the POI is located
- `longitude` & `latitude` - Geographic coordinates
- `Type Code` - Category (NATURE, HISTORY AND CULTURE, SHOPPING)
- `gmaps_rating` - Google Maps rating
- `categories` - Additional categorization

## Notebooks

### scraper.ipynb
Main scraping notebook with full implementation of the image downloader. Processes all 94 POIs from the CSV file.

### notebook.ipynb
Contains basic examples and test cases for scraping functionality, including simple queries and small-scale downloads.

### diagnosis.ipynb
Utility notebook for validating downloaded images, detecting corrupted files, and cleaning up the image directory.

## Statistics

- **Total POIs**: 94 locations
- **Images per POI**: Up to 10
- **Potential Downloads**: ~940 images
- **Estimated Size**: ~459 MB

## Image Filtering

The scraper automatically filters out:
- WebP format images (by URL extension)
- WebP format images (by Content-Type header)
- Images that fail to download due to network errors
- Images from forbidden or protected sources

## Error Handling

The scraper handles various errors gracefully:
- Network timeouts (10-second timeout)
- HTTP errors (403, 404, 460, etc.)
- Invalid image files
- Redirect loops

## License

This project is for educational and research purposes. Please respect copyright and terms of service of image sources.

## Notes

- Images are saved with descriptive filenames: `{POI_name}_{municipality}_Bulacan_{index}.jpg`
- The scraper includes progress tracking via `clear_output()` in Jupyter notebooks
- Broken images can be detected and removed using the diagnosis utilities
