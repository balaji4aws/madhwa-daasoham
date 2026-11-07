# Madhwa Daasoham: dataset card for LLMs

This repository is a dataset: one CSV file, `daasoham_videos.csv`, cataloging every video on the YouTube channel [Daasoham](https://www.youtube.com/@daasoham). The channel publishes Haridasa / Madhwa devotional music: Kannada *dasara padagalu* (songs of the Haridasas), Sanskrit stotras, chants, and serialized readings. Most songs are sung by Venugopal Khatavkar. This card describes what the data contains and how to answer questions from it correctly.

## Grounding rules (read first)

Answer **only from `daasoham_videos.csv`**. Do not hallucinate.

1. **No invented facts.** Never invent or guess a video, title, URL, composer, singer, upload date, view count, language or lyric line. If the CSV doesn't contain it, say "not in this dataset".
2. **Empty means unknown.** Do not fill an empty cell from general knowledge. For example, don't name a composer the channel didn't credit. If you add outside knowledge, label it clearly as *not from this dataset*.
3. **Quote lyrics verbatim** from the `lyrics_*` columns, in the script they're in. Don't complete, correct, or re-transliterate them. Don't present a translation or meaning as if it were in the data; this dataset has no translations.
4. **Compute counts, don't estimate.** Counts, totals, "most viewed" and date windows must come from filtering the rows. If you can't run the filter, say so instead of giving a number.
5. **Cite every video** with its `url` (or `video_id`) so the answer can be checked.
6. **Show uncertainty.** `composer`, `singer`, `music_by`, `song_language` and the `lyrics_*` split are derived from free text. If the answer depends on them, mention `song_language_basis` / `composer_credit_text`, or check `description`.
7. **Data is a snapshot.** Views, likes and the video list are as of the snapshot date below. Say so when answering about "latest" or "current".
8. **Zero results is a valid answer.** If no row matches, say "none in this dataset" and, if useful, show the closest matches with how they differ. Do not stretch a filter to produce an answer.

## File

- `daasoham_videos.csv`: UTF-8 with BOM, comma-separated, header row.
- **One row = one YouTube video** (regular video or Short). `video_id` is the unique key.
- Lyrics and description cells span many lines inside double quotes. Read the file with a CSV parser. The file has many more text lines than rows.
- Text is in Kannada, Telugu, Tamil, Devanagari, Malayalam and Latin scripts.
- An empty cell means the video's title/description does not state it. It does not mean the thing does not exist.

## What's in it (snapshot 2026-11-07)

**Size:** 479 videos (463 regular, 16 Shorts), uploaded 2019-08-11 to 2026-11-01.

**Uploads by year:** 2019: 16 · 2020: 30 · 2021: 108 · 2022: 61 · 2023: 93 · 2024: 70 · 2025: 64 · 2026: 37

**Length:** median 4 min 29 s. Up to 2 min: 55 · 2 to 7 min: 294 (typical sung song) · 7 to 20 min: 95 · over 20 min: 35 (long recitations, discourses, serialized parts such as *Sumadhwa Vijaya*, *Venkatesha Parijatha*, *Harikathamrutasara*, *Bhagavad Gita*).

**Views:** 141 million in total. Median 22,573 per video. The most-viewed are:

| Views | Title |
|---|---|
| 21.2 M | Sri Raghavendra Akshara Malika Stotra |
| 13.9 M | Sri Raghavendra Stotra (Shree Poornabodha Guru) |
| 13.5 M | Gajendra Moksha (lyrical video), Sri Vadirajaru |
| 8.2 M | Gajendra Moksha, Sri Vadirajaru |
| 6.2 M | Dasanagu Visheshanagu, Kanakadasaru |

**Sung language** (`song_language`): Kannada 226 · Sanskrit 167 · Telugu 4 · Hindi 1 · Unknown 81. No Marathi songs.

**Composer stated** in 304 of 479 videos. Most frequent:

| Composer | Videos | Sung language |
|---|---|---|
| Prasanna Vitthala Dasa (Pt. Vadirajacharya Karanam) | 50 | Kannada 29, Sanskrit 18, Telugu 3 |
| Vadirajaru (Sri Vadiraja Tirtha) | 27 | Kannada 12, Sanskrit 7, Telugu 1, Unknown 7 |
| Jagannatha Dasa | 27 | Kannada 24, Sanskrit 3 |
| Purandara Dasa | 21 | Kannada 21 |
| Vijaya Dasa | 21 | Kannada 21 |
| Harapanahalli Bheemavva | 17 | Kannada 17 |
| Narayana Panditacharya | 14 | Sanskrit 14 (Sumadhwa Vijaya) |
| Madhwacharya | 12 | Sanskrit 10, Kannada 1, Unknown 1 |
| Prasanna Venkata Dasa, Vyasarajaru, Sri Gokavi Anantaryaru | 6 each | |
| Kanaka Dasa, Sripadarajaru, Sri Satyatma Teertharu | 4 each | |

89 distinct composer values in total.

**Lyrics:** 204 videos have lyrics in the description. Coverage by script:

| Column | Videos |
|---|---|
| `lyrics_english` (Latin transliteration) | 165 |
| `lyrics_kannada` | 144 |
| `lyrics_devanagari` | 92 |
| `lyrics_telugu` | 55 |
| `lyrics_tamil` | 43 |
| `lyrics_malayalam` | 5 |

Many videos give the same lyrics in several scripts: 88 videos carry 3 or more. In addition, 18 videos link a lyrics PDF, 28 say their lyrics are in the subtitles (CC), and 180 titles say "With Lyrics" (usually on-screen lyrics). 286 videos have no lyrics text in the description.

## Columns

| Column | Type | Meaning |
|---|---|---|
| `video_id` | string | YouTube video ID (unique) |
| `url` | string | Watch link |
| `type` | `video` / `short` | Regular video or Short. Shorts are often excerpts or teasers of a full video |
| `title` | string | Full title |
| `song_name` | string | Title up to the first `\|` |
| `details_fetched` | `yes` / `no` | If `no`, only title, views and duration are available |
| `on_channel` | `yes` / `no` | `no` = later removed or made private. Last known data is kept |
| `upload_date` | date | `YYYY-MM-DD` |
| `views`, `likes`, `comments` | integer | As of the snapshot |
| `duration_sec` | integer | Length in seconds |
| `composer` | string | Who **wrote** it. Standard name where known, otherwise as credited |
| `composer_found_in` | string | Where the credit is: `description: written by` (the channel's explicit credit), `title` or `description` |
| `composer_credit_text` | string | The credit exactly as the channel wrote it |
| `singer` | string | Who sings |
| `music_by` | string | Who set the music / tune |
| `song_language` | `Kannada` / `Sanskrit` / `Telugu` / `Hindi` / `Unknown` | Language the song is **sung** in |
| `song_language_basis` | string | Evidence for `song_language`. Starts with `channel says:` when the channel states the language itself (strongest). Otherwise it names the clue used: lyric words, title words, a stotra-type title, or a Haridasa composer |
| `lyrics_in_description` | `yes` / `no` | Description has lyrics text or a lyrics PDF link |
| `lyrics_scripts` | string | Scripts the description's lyrics are typed in, `; `-separated |
| `lyrics_kannada` … `lyrics_malayalam` | text | Lyrics typed in that script |
| `lyrics_english` | text | Lyrics in Latin letters (transliteration, not a translation) |
| `lyrics_pdf_link` | string | Lyrics PDF link(s) |
| `lyrics_in_subtitles` | `yes` / `no` | Channel says lyrics are in subtitles (CC). Those lyrics are not in this file |
| `title_says_with_lyrics` | `yes` / `no` | Title says "With Lyrics" (usually on-screen in the video) |
| `title_scripts` | string | Scripts used in the title |
| `tags` | string | YouTube tags, `; `-separated |
| `first_seen` | date | When the video first appeared in this dataset |
| `description_changed_on`, `title_changed_on` | date | Latest snapshot that found the description / title edited. Empty = unchanged |
| `description` | text | Full description (ground truth for every parsed column) |

## Rules for answering

1. **Script is not language.** `lyrics_telugu` filled in means the lyrics are *typed* in Telugu script. The song can still be Kannada; the channel often types one Kannada song in 4 or 5 scripts. "Telugu song" → `song_language = Telugu`. "Telugu lyrics" → `lyrics_telugu` non-empty.
2. **Match composers on `composer` first.** Then search `title`, `composer_credit_text` and `description` in all scripts for spellings: Purandara Dasa = Purandara Dasaru = Purandararu = ಪುರಂದರ = పురందర.
3. **"By" is not "about".** "Jayateertha Ashtakam" and "Vadiraja Stuti" are songs *about* those saints, not *by* them.
4. **Two Vadirajas:** `Vadirajaru` is the 16th-century saint. `Prasanna Vitthala Dasa (Pt. Vadirajacharya Karanam)` is a modern composer whose pen name is Prasanna Vitthala. Keep them separate.
5. **No lyrics here ≠ no lyrics.** Lyrics may only be on screen, in subtitles or in a PDF. Say which.
6. **Time windows** ("last 6 months") use `upload_date`, counted back from today's date.
7. **Song vs recitation:** sung songs are mostly 2 to 7 minutes. Stotras, chants (*japa*), discourses and numbered serial parts are separate kinds of content.
8. **State whether Shorts are included** in counts and lists.
9. `composer`, `singer`, `music_by`, `song_language` and the `lyrics_*` columns are derived from free text. When a precise answer matters, confirm against `description`.
10. **Always include `url`** when listing videos.

## Example filters

| Question | Filter |
|---|---|
| Purandara Dasa songs from the last 6 months with Telugu lyrics | `composer = Purandara Dasa` AND `upload_date >= today − 6 months` AND `lyrics_telugu` non-empty |
| Songs sung in Telugu | `song_language = Telugu` |
| Short Kannada songs with Kannada lyrics | `song_language = Kannada` AND `lyrics_kannada` non-empty AND `duration_sec` 120 to 420 |
| Most-viewed Vijaya Dasa songs | `composer = Vijaya Dasa`, sort by `views` descending |
| Lyrics only in subtitles | `lyrics_in_subtitles = yes` AND `lyrics_in_description = no` |
| Recently changed or added | `description_changed_on` / `title_changed_on` non-empty, or latest `first_seen` |

## Source

How the columns are derived is described in `DESIGN.md`. For answering questions, this README and the CSV are enough.

Public YouTube metadata of the Daasoham channel: titles, descriptions, tags, counts and dates. Nothing is transcribed from audio. All content belongs to the Daasoham channel. This dataset is unofficial.
