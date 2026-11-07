# Madhwa Daasoham

An unofficial, machine-readable catalog of every video on the [Daasoham YouTube channel](https://www.youtube.com/@daasoham). The channel posts Haridasa / Madhwa devotional songs and stotras.

There's one file: **`daasoham_videos.csv`**. It has one row per video (regular videos and Shorts) and is UTF-8 encoded, so Kannada, Telugu, Tamil and Devanagari text show correctly. It's built for spreadsheets and LLMs, and is refreshed about once a month.

## Columns

| Column | Meaning |
|---|---|
| `video_id`, `url` | YouTube ID and watch link |
| `type` | `video` or `short` |
| `title`, `song_name` | Full title, and the first segment of the title before any `\|` |
| `details_fetched` | `no` means only title, views and duration are available for that video; the other columns are blank |
| `upload_date` | YYYY-MM-DD |
| `views`, `likes`, `comments`, `duration_sec` | Counts at the latest refresh |
| `composer` | Canonical composer name, e.g. `Purandara Dasa` covers Purandara Dasaru / Purandararu / ಪುರಂದರ / పురందర |
| `composer_found_in` | Where the composer came from: `description: written by` (the channel's credit line, most reliable), `description` or `title` |
| `composer_credit_text` | The raw "Written by ..." credit, as written by the channel |
| `singer`, `music_by` | Taken from credit lines such as "sung by", "vocals:", "rendition by" |
| `song_language` | Best guess at the language the song is **sung** in: Kannada, Sanskrit, Telugu, Hindi, or Unknown |
| `song_language_basis` | The evidence behind that guess, e.g. `channel says: "Telugu song ..."`, `lyrics read as Sanskrit`, `title says stotra/ashtakam` |
| `lyrics_in_description` | `yes` if the description contains lyrics text or a lyrics PDF link |
| `lyrics_scripts` | Scripts the description's lyrics are typed in |
| `lyrics_kannada` | Lyrics typed in Kannada script, if given |
| `lyrics_telugu` | Lyrics typed in Telugu script, if given |
| `lyrics_tamil` | Lyrics typed in Tamil script, if given |
| `lyrics_devanagari` | Lyrics typed in Devanagari script (usually Sanskrit), if given |
| `lyrics_malayalam` | Lyrics typed in Malayalam script, if given |
| `lyrics_english` | Lyrics written in English letters (transliteration), if given |
| `lyrics_pdf_link` | Google Drive / Docs link to lyrics, if any |
| `lyrics_in_subtitles` | `yes` if the channel says the lyrics are in the video's subtitles (CC) |
| `title_says_with_lyrics` | `yes` if the title contains "With Lyrics". On this channel that usually means the lyrics appear on screen in the video |
| `title_scripts` | Scripts used in the title |
| `tags` | YouTube tags |
| `first_seen` | Date the video first appeared in this catalog |
| `description_changed_on`, `title_changed_on` | Date of the latest refresh that found the description / title edited (blank = unchanged) |
| `description` | The video description, so any parsed field can be checked |

## Caveats

- Composer, singer, music, language and lyrics columns are parsed from free-text descriptions. They're good, not perfect. When it matters, check against `description`.
- **Script is not language.** The channel often types the same lyrics in several scripts, so a Kannada song can have `lyrics_telugu` filled in. `song_language` is the sung language; the `lyrics_*` columns are scripts.
- `song_language` is inferred from text, not from listening to the audio. Telugu is only marked when the channel itself says so; otherwise a Kannada/Telugu-type lyric is marked Kannada, the channel's main language.
- The uploader doesn't always include lyrics, or every script. Blank lyrics columns mean the description doesn't have them, not that the song has none.
- Views and likes are a snapshot and will drift over time.

All content belongs to the Daasoham channel. This repo holds only public metadata and links.
