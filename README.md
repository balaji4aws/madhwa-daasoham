# Madhwa Daasoham: dataset card for LLMs

This repository is a dataset. It has one data file, `daasoham_videos.csv`, which catalogs every video on the YouTube channel [Daasoham](https://www.youtube.com/@daasoham) (Haridasa / Madhwa devotional songs, stotras and discourses). This README tells a language model how to read that file and answer questions from it correctly.

## File format

- File: `daasoham_videos.csv`. Encoding UTF-8 with BOM. Comma-separated, with a header row.
- Grain: **one row = one YouTube video** (regular video or Short). `video_id` is unique.
- Multi-line cells (lyrics, description) are wrapped in double quotes. Parse with a real CSV parser. Never count lines to count rows.
- Text is in Kannada, Telugu, Tamil, Devanagari, Malayalam and Latin scripts.
- An empty cell means "not stated in the video's description or title". It does not mean "does not exist".
- Snapshot: 479 videos (463 videos + 16 Shorts), uploaded 2019-08-11 to 2026-11-01, fetched 2026-11-07. Newer rows may be added over time.

## Columns

| Column | Type | Values / meaning |
|---|---|---|
| `video_id` | string | YouTube video ID (unique key) |
| `url` | string | `https://www.youtube.com/watch?v=<video_id>` |
| `type` | enum | `video` or `short` |
| `title` | string | Full YouTube title |
| `song_name` | string | First segment of `title` before any `\|` |
| `details_fetched` | enum | `yes` / `no`. If `no`, only title, views and duration are reliable |
| `on_channel` | enum | `yes`, or `no` if the video was later removed or made private (last known data kept) |
| `upload_date` | date | `YYYY-MM-DD` |
| `views`, `likes`, `comments` | integer | Counts as of the snapshot |
| `duration_sec` | integer | Length in seconds (31 to 3488) |
| `composer` | string | Who **wrote** the song. Canonical name where known (see composer names below), otherwise the credit as written. Empty = not stated |
| `composer_found_in` | enum | `description: written by` (the channel's credit line, most reliable), `title`, `description`, or empty |
| `composer_credit_text` | string | The raw "Written by ..." credit text |
| `singer` | string | Who sings. Mostly `Venugopal Khatavkar`; empty = not stated |
| `music_by` | string | Who set the tune / music. Empty = not stated |
| `song_language` | enum | Language the song is **sung** in: `Kannada`, `Sanskrit`, `Telugu`, `Hindi`, `Unknown` |
| `song_language_basis` | string | Evidence for `song_language` (see "How song_language was decided") |
| `lyrics_in_description` | enum | `yes` if the description contains lyrics text or a lyrics PDF link |
| `lyrics_scripts` | string | `; `-separated scripts the lyrics are typed in |
| `lyrics_kannada` | text | Lyrics typed in Kannada script |
| `lyrics_telugu` | text | Lyrics typed in Telugu script |
| `lyrics_tamil` | text | Lyrics typed in Tamil script |
| `lyrics_devanagari` | text | Lyrics typed in Devanagari script (usually Sanskrit) |
| `lyrics_malayalam` | text | Lyrics typed in Malayalam script |
| `lyrics_english` | text | Lyrics written in Latin letters (transliteration, not translation) |
| `lyrics_pdf_link` | string | Google Drive / Docs link(s) to lyrics, space-separated |
| `lyrics_in_subtitles` | enum | `yes` if the channel says lyrics are in the video's subtitles (CC). Those lyrics are **not** in this file |
| `title_says_with_lyrics` | enum | `yes` if the title says "With Lyrics". Usually means lyrics are shown on screen in the video, not necessarily in the description |
| `title_scripts` | string | Scripts used in the title |
| `tags` | string | `; `-separated YouTube tags |
| `first_seen` | date | Date the video first appeared in this dataset |
| `description_changed_on` | date | Latest snapshot date on which the description was found edited; empty = never changed |
| `title_changed_on` | date | Same, for the title |
| `description` | text | The video's description (source of all parsed fields) |

## Rules for answering questions

1. **Script is not language.** `lyrics_telugu` filled in means the lyrics are *typed* in Telugu script. It does **not** mean the song is in Telugu. The channel often types the same Kannada lyrics in four or five scripts. For "Telugu song", filter `song_language = Telugu`. For "Telugu lyrics available", filter `lyrics_telugu` is non-empty.
2. **Composer matching.** Match on `composer` (canonical) first. For partial or alternative spellings, also search `title`, `composer_credit_text` and `description`, case-insensitive, in all scripts. Example: Purandara Dasa = Purandara Dasaru = Purandararu = ಪುರಂದರ = పురందర.
3. **Composer, not subject.** `composer` is who *wrote* the song. A song *about* a saint (e.g. "Jayateertha Ashtakam", "Vadiraja Stuti") is not *by* that saint.
4. **Two different Vadirajas.** `Vadirajaru` is the 16th-century saint Sri Vadiraja Tirtha. `Prasanna Vitthala Dasa (Pt. Vadirajacharya Karanam)` is a modern composer. Never merge them.
5. **"Has lyrics"** = `lyrics_in_description = yes`, or the specific `lyrics_<script>` column is non-empty. Lyrics only shown in the video or in subtitles are not in this file. Say so, rather than claiming there are no lyrics.
6. **Dates.** Use `upload_date` for "uploaded in the last N months", counted back from today's date. Use `first_seen` only for "added to this dataset".
7. **Sung songs vs other content.** The channel also has stotras, chants (`japa`), discourses and serialized readings (e.g. "Sumadhwa Vijaya - 14", "Venkatesha Parijatha 6 - 3"). `duration_sec` and the title help tell a short sung song (about 2 to 7 minutes) from long recitations.
8. **Uncertain fields.** `composer`, `singer`, `music_by`, `song_language` and the `lyrics_*` split are parsed from free text. When an answer depends on them, cite the `url`, and check against `description` or `song_language_basis` if in doubt.
9. **Shorts.** `type = short` clips are often excerpts of a full video. Include or exclude them deliberately, and say which.
10. Always give the `url` when listing videos.

## How song_language was decided (in priority order)

1. **`channel says: "..."`.** The title or description names the language (e.g. "Telugu song in Abhang style", "Dasarapada in Kannada"). Most reliable.
2. **`lyrics read as Sanskrit` / `lyrics read as Kannada/Telugu-type`.** From word endings in the description's lyrics. A Kannada/Telugu-type lyric is labeled `Kannada`, because the channel marks its Telugu songs explicitly.
3. **`lyrics in English letters read as ...`.** The same check on transliterated lyrics.
4. **`title words read as ...`.** The same check on Indic-script words in the title.
5. **`title says stotra/ashtakam/etc.`.** The title names a Sanskrit form.
6. **`Haridasa composer / Kannada song form`.** A Dasa composer, or a Kannada form (suladi, haadu, shobhane).
7. Otherwise `Unknown`, with an empty basis.

Snapshot counts: Kannada 226, Sanskrit 167, Unknown 81, Telugu 4, Hindi 1. The data has no Marathi song.

## Composer names used in `composer`

Purandara Dasa · Vijaya Dasa · Jagannatha Dasa · Gopala Dasa · Kanaka Dasa · Vadirajaru · Sripadarajaru · Vyasarajaru · Prasanna Venkata Dasa · Mahipati Dasa · Helavanakatte Giriyamma · Harapanahalli Bheemavva · Kamalesha Vittala · Shyamasundara Dasa · Prananatha Vitthala · Ambabai · Madhwacharya · Trivikrama Panditacharya · Narayana Panditacharya · Raghavendra Tirtha · Jayatirtha · Prasanna Vitthala Dasa (Pt. Vadirajacharya Karanam). Other composers appear as written by the channel (e.g. `Sri Satyatma Teertharu`, `Karpara Narahari Dasaru`).

## Example questions and how to filter

| Question | Filter |
|---|---|
| Purandara Dasa songs uploaded in the last 6 months with Telugu lyrics | `composer = Purandara Dasa` AND `upload_date >= today - 6 months` AND `lyrics_telugu` non-empty |
| Songs actually sung in Telugu | `song_language = Telugu` |
| Short Kannada songs with Kannada lyrics in the description | `song_language = Kannada` AND `lyrics_kannada` non-empty AND `duration_sec` between 120 and 420 |
| Most-viewed Vijaya Dasa songs | `composer = Vijaya Dasa`, sort by `views` descending |
| Videos whose lyrics are only in subtitles | `lyrics_in_subtitles = yes` AND `lyrics_in_description = no` |
| What changed recently | `description_changed_on` or `title_changed_on` non-empty, or `first_seen` = latest snapshot date |

## Provenance

All values come from public YouTube metadata (title, description, tags, counts, dates) of the Daasoham channel. Nothing is transcribed from audio. All content belongs to the Daasoham channel. This dataset is unofficial.
