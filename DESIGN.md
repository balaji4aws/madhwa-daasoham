# Madhwa Daasoham: design

How `daasoham_videos.csv` is built and kept up to date. For what the data contains and how to query it, see `README.md`.

## Goal

One exhaustive, machine-readable table of every video on the YouTube channel [Daasoham](https://www.youtube.com/@daasoham). Each video's free-text description is split into proper columns (composer, singer, music, sung language, lyrics per script), so the table can answer questions such as "Purandara Dasa songs from the last six months with Telugu lyrics".

## Principles

- **Text only.** Every value comes from public YouTube metadata: title, description, tags, counts and dates. Nothing is transcribed from audio, and subtitles are not downloaded.
- **Keep the source.** The full `description` is kept in every row, so any parsed column can be checked against it.
- **Empty means not stated.** A column is left blank rather than guessed.
- **Evidence over inference.** Where a value is inferred (sung language, composer), a companion column records what it was based on (`song_language_basis`, `composer_found_in`, `composer_credit_text`).
- **One row per video**, keyed by `video_id`. Videos are never deleted from the table; removed ones are flagged with `on_channel = no`.

## Pipeline

1. **List** every item on the channel's Videos and Shorts tabs (the channel has no live-streams tab). Shorts are tagged `type = short`.
2. **Fetch metadata** for each video: title, description, views, likes, comments, upload date, duration, tags, plus the time of the fetch. Requests are paced to stay well within YouTube's limits: small batches, a few seconds between videos, and a few minutes between batches. Fetching resumes where it left off if interrupted.
3. **Store** every fetch in an append-only log, so earlier versions of a description are never lost.
4. **Parse** the latest fetch of each video into the CSV columns, using the rules below.
5. **Check** the new video rows against their descriptions before publishing.

## Parsing rules

### Composer

Checked in this order:

1. **The channel's credit line**: "Written by …", "Composed by …" (unless it continues "and sung"), "Composition of …", "Lyrics by …". Honorifics such as *Pt.*, *Sri.*, *Dr.* don't end the name.
2. **An authorship cue elsewhere**: "by X", "X virachita", ರಚಿತ.
3. **A credit segment in the title**, e.g. `… | Sri Purandara Dasaru |`. A name inside the song title itself does not count.

A name that only appears inside the lyrics is ignored, because it is usually the subject of the song, not its author. Phrases like "in praise of X", "X Stuti", "X Ashtakam", "X Munivara" or "X Dasara …" (genitive) mark X as the subject.

Names are mapped to one standard form, e.g. Purandara Dasaru / Purandararu / ಪುರಂದರ / పురందర → `Purandara Dasa`. The modern composer **Pt. Vadirajacharya Karanam** (pen name *Prasanna Vitthala Dasa*) is matched before the 16th-century saint **Sri Vadirajaru**, so the two are never confused.

### Singer and music

These come from credit lines: "sung by", "vocals:", "rendition by", "Music and vocals:", "Composed and sung by", and lines starting with "Music:" / "Music by". Streaming-store headers (e.g. "Apple music:") and links are never taken as names. Variant spellings of the same artist are merged, e.g. Venugopal K / Dr. Venugopal K → `Venugopal Khatavkar`.

### Lyrics by script

- Each description line goes to its dominant script: Kannada, Telugu, Tamil, Devanagari, Malayalam, or Latin.
- Links, store lines and short credit lines are dropped.
- A Latin-script line counts as a lyric line if it carries verse markers (`..pa..`, `||`, `॥`, `..1..`) or diacritics, or reads like verse rather than an English sentence.
- A script's column (`lyrics_kannada`, `lyrics_telugu`, …, `lyrics_english`) is filled only if it has at least 3 lyric lines (4 for Latin) and enough text, so a one-line title in Kannada isn't treated as lyrics.
- Lyrics are copied exactly as written, never corrected or re-transliterated.

### Sung language

The first rule that applies sets `song_language`, and `song_language_basis` records which one:

1. **The channel states it**, e.g. "Telugu song in Abhang style", "Dasarapada in Kannada", "(Hindi)", or "- Telugu" as a title segment. Phrases about scripts ("lyrics in Telugu", "subtitles in …") are ignored.
2. **Word endings in the lyrics.** Sanskrit words often end in a consonant or visarga (ಮ್, ः); Kannada and Telugu words end in vowels such as ು, ೆ, ೊ. A Kannada/Telugu-type lyric is labeled Kannada, because the channel explicitly marks its Telugu songs.
3. **The same check on transliterated lyrics.**
4. **The same check on Indic-script words in the title.**
5. **A Sanskrit form in the title**: stotra, ashtakam, gadya, japa, suprabhata, etc.
6. **A Haridasa composer, or a Kannada song form** (suladi, haadu, shobhane).
7. Otherwise `Unknown`.

This was checked against 12 songs whose language is known: 12 of 12 correct.

## Change tracking

Each refresh re-fetches every video and appends the result to the log. Consecutive fetches of the same video are compared:

- `first_seen`: date of the first fetch of that video.
- `description_changed_on` / `title_changed_on`: latest fetch where the text differed from the previous fetch.
- `on_channel = no`: the video no longer appears in the channel's listing.

Views, likes and comments always reflect the latest fetch.

## Refresh

Refreshes are run manually, roughly monthly. A refresh:

1. Re-lists the channel, which picks up new uploads.
2. Re-fetches every video, with the same pacing as above.
3. Rebuilds the CSV.
4. Prints a change report: row count, total views, and new, removed and edited videos with links.
5. Includes a review of new rows against their descriptions before the update is committed.

## Known limitations

- `song_language` is inferred from text, not from listening. Telugu is detected only when the channel says so.
- 81 videos have `Unknown` language (mostly chants, discourses and videos without lyrics). 175 have no composer stated.
- Lyrics that are only shown on screen or only in subtitles are not captured.
- Parsing rules are tuned to this channel's writing style and may need updating if the channel changes how it writes descriptions.
