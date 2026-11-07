# How this dataset is made

This page explains, in plain terms, how `daasoham_videos.csv` is put together. For what's inside the file and how to use it, see `README.md`.

## The idea

The [Daasoham](https://www.youtube.com/@daasoham) YouTube channel has hundreds of devotional songs. Each video's description often says who wrote the song, who sang it, and sometimes gives the full lyrics, all in free-flowing text. That's hard to search. This project turns those descriptions into a clean table: one row per video, with separate columns for each piece of information.

## The steps

**1. List every video.** We read the channel's video list and Shorts list to get the ID of every video.

**2. Collect the details.** For each video we collect what YouTube shows publicly: title, description, upload date, length, views, likes, comments and tags. We go slowly, a few videos at a time with pauses in between, so we don't overload YouTube.

**3. Sort the description into columns.** The description is plain text, so we read it line by line and pick out:

- **Composer:** from the channel's own credit line, such as "Written by Sri Purandara Dasaru". Different spellings of the same name are merged into one, so ಪುರಂದರ, Purandararu and Purandara Dasaru all become *Purandara Dasa*. A saint who is only *mentioned* in a song isn't counted as its writer.
- **Singer and music:** from lines like "Sung by …" or "Music: …".
- **Lyrics:** lines of verse are grouped by the alphabet they're written in, so Kannada, Telugu, Tamil, Devanagari, Malayalam and English letters each get their own column. Lyrics are copied exactly as written.
- **Language of the song:** if the channel says it ("Telugu song"), we use that. Otherwise we look at the lyrics or title. Sanskrit and Kannada words end in different ways, which is a reliable clue. The CSV also records *why* each language was chosen.

**4. Double-check.** We spot-check the results against the original descriptions, and we tested the language step on songs whose language we already knew.

**5. Publish.** The table is saved as one CSV file. The full original description stays in every row, so anyone can verify a value.

## Keeping it up to date

About once a month we run the same steps again. This adds new videos, updates view counts, and records the date if a video's title or description was edited. Videos that disappear from the channel stay in the table, marked as no longer on the channel.

## What we don't do

- We don't listen to or transcribe any audio. Everything comes from text the channel already published.
- We don't guess. If the description doesn't say something, the cell stays empty.
- Lyrics that only appear on screen or in subtitles aren't included.

## Limits

The sorting is done by rules, not by a person reading every video, so a few values may be off. When something matters, check the `description` column. The language of a song is a well-informed guess from its text, not a check against the audio.
