# Madhwa Daasoham

An unofficial, machine-readable catalog of every video on the [Daasoham YouTube channel](https://www.youtube.com/@daasoham). The channel posts Haridasa / Madhwa devotional songs and stotras.

There's one file: **`daasoham_videos.csv`**. It has one row per video and is UTF-8 encoded, so Kannada, Telugu and Devanagari text show correctly. It's built for spreadsheets and LLMs.

## Columns

| Column | Meaning |
|---|---|
| `video_id`, `url` | YouTube ID and watch link |
| `type` | `video` or `short` |
| `title`, `song_name` | Full title, and the first segment of the title before any `\|` |
| `details_fetched` | `no` means only title, views and duration are available for that video; description, date and lyrics columns are blank |
| `upload_date` | YYYY-MM-DD |
| `views`, `likes`, `comments`, `duration_sec` | Counts at the time of the snapshot |
| `composer` | Canonical composer name, e.g. `Purandara Dasa` covers Purandara Dasaru / Purandararu / ಪುರಂದರ / పురందర |
| `composer_found_in` | Where the composer came from: `description: written by` (the channel's credit line, most reliable), `description` or `title` |
| `composer_credit_text` | The raw "Written by ..." credit, as written by the channel |
| `singer`, `music_by` | Taken from credit lines such as "sung by", "vocals:", "rendition by" |
| `lyrics_in_description` | `yes` if the description contains lyrics text or a lyrics PDF link |
| `lyrics_scripts` | The script the lyrics are typed in: Kannada, Telugu, Devanagari, Tamil, Malayalam, Roman (transliteration) |
| `lyrics_pdf_link` | Google Drive / Docs link to lyrics, if any |
| `title_says_with_lyrics` | `yes` if the title contains "With Lyrics". On this channel that usually means the lyrics appear on screen in the video, not in the description |
| `title_scripts` | Scripts used in the title |
| `tags` | YouTube tags |
| `lyrics_text` | The lyrics part of the description, with links and credits removed |
| `description` | The video description, so any parsed field can be checked |

## Caveats

- The composer, singer, music and lyrics columns are parsed from free-text descriptions. They're good, not perfect. When it matters, check against `description`.
- `lyrics_scripts` is the script the lyrics are **typed in**, not the language the song is sung in. A Kannada song can have its lyrics typed in Telugu script.
- Views and likes are a snapshot and will drift over time.

All content belongs to the Daasoham channel. This repo holds only public metadata and links.
