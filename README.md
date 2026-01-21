# Scrape Goat: Image and Description Scraper

A collection of Jupyter notebooks for scraping Bulacan Points of Interest (POI) images with DuckDuckGo, tracking download health, and drafting textual descriptions.

## Overview

The workflow pulls POI names and municipalities from [data/poi.csv](data/poi.csv), generates search terms, and downloads multiple images per location via DuckDuckGo. Supplementary notebooks validate image integrity and summarize web content related to each POI.

## Project Structure

```
.
├── image-scraper.ipynb      # Bulk image scraping pipeline
├── notebook.ipynb           # Exploratory scraping examples
├── diagnostic.ipynb         # Download counting and integrity checks
├── description-scraper.ipynb# POI description drafting
├── data/
│   ├── poi.csv
│   └── poi_backup.csv
├── downloaded_images/
├── test_images/
└── README.md
```

## Features

- **Automated Image Scraping**: [`download_images`](image-scraper.ipynb) fetches up to 10 results per query.
- **CSV-driven Queries**: [`generate_search_queries`](image-scraper.ipynb) builds `"POI in Municipality Bulacan"` prompts from POI data.
- **Progress Feedback**: Looping logic in [image-scraper.ipynb](image-scraper.ipynb) surfaces per-query status updates.
- **Broken Image Detection**: [diagnostic.ipynb](diagnostic.ipynb) verifies downloads and deletes corrupt files with Pillow.
- **Description Summaries**: [`search_and_summarize`](description-scraper.ipynb) blends DuckDuckGo snippets and Transformer summarization.

## Requirements

Install core dependencies:

```sh
pip install requests ddgs pandas Pillow
```

Optional NLP tooling for [description-scraper.ipynb](description-scraper.ipynb):

```sh
pip install transformers torch
```

## Usage

### Bulk Image Download

```python
from ddgs import DDGS
import pandas as pd

# Prepare search phrases
df = pd.read_csv("data/poi.csv")[["name", "municipality"]]
df_dict = df.to_dict(orient="records")
search_queries = generate_search_queries(df_dict)

# Download images
for query in search_queries:
    download_images(query, max_results=10)
```

### Validate Downloads

[diagnostic.ipynb](diagnostic.ipynb) counts files, flags corrupted images with Pillow, and removes them automatically.

### Draft POI Summaries

```python
summary = search_and_summarize("Mt. Balagbag in San Jose Del Monte Bulacan", max_results=5)
print(summary)
```

## Data Format

Key columns in [data/poi.csv](data/poi.csv):

- `name` – POI name  
- `municipality` – Municipality of the POI  
- `longitude`, `latitude` – Coordinates  
- `Type Code`, `categories` – Classification metadata  
- `gmaps_rating` – Google Maps rating snapshot

## Notes

- Image files are saved as `{POI_name}_{municipality}_Bulacan_{index}.jpg`.
- Broken downloads are reported and skipped; WebP references are ignored by URL and `Content-Type`.
- `.gitignore` excludes downloaded image folders and CSV exports by default.
