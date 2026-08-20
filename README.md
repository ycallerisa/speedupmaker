# SpeedupMaker

SpeedupMaker is an experimental Python media pipeline that converts a Spotify track URL into a short vertical video with sped-up audio and word-level synchronized lyrics.

The project was created to automate a multi-stage workflow involving media acquisition, lyrics retrieval, forced alignment, timestamp transformation and video rendering. It is currently an **environment-specific prototype**, not a turnkey Python package.

## Pipeline

```text
Spotify track URL
      |
      +--> spotDL ----------------------> song.mp3
      |
      +--> Genius ----------------------> cleaned lyrics
                                              |
                                              v
                              NUS AutoLyrixAlign / Kaldi
                                              |
                                              v
                                  word-level timestamps
                                              |
                   +--------------------------+
                   |
                   +--> audio speed transform
                   +--> timestamp transform
                   +--> background composition
                                              |
                                              v
                                    final_output.mp4
```

## Implemented capabilities

- validation of Spotify track URLs;
- track download through `spotdl`;
- lyrics retrieval through the Genius API;
- normalization and cleanup of lyrics;
- forced alignment through NUS AutoLyrixAlign running in a Singularity/Kaldi image;
- conversion of alignment output to word-level JSON timestamps;
- reconstruction of sentence-level lyric groups;
- synchronized transformation of audio speed and lyric timestamps;
- background-video concatenation and cropping;
- rendering of a 1080x1920 vertical video with per-word highlighting.

## Repository layout

| File | Responsibility |
| --- | --- |
| `full_script.py` | Pipeline orchestration, alignment parsing, audio processing and rendering |
| `lyrics_fetcher.py` | Genius API access and lyrics preprocessing |

Generated files include `song.mp3`, `song_cut.mp3`, `lyrics.json`, `sentence_based_lyrics.json`, `lyrics_speed_up.json`, `song_speed_up.wav`, `out.mp4` and `final_output.mp4`.

## Requirements

The current prototype expects:

- Python 3.10 or later;
- FFmpeg available on `PATH`;
- `spotdl`;
- Singularity or a compatible Apptainer setup;
- a local `NUSAutoLyrixAlign` directory and its `kaldi.simg` image;
- a `Montserrat-Bold.ttf` font file;
- one or more background videos in `video_background/`;
- a Genius API access token.

Python packages used by the scripts include:

```bash
python -m pip install spotdl lyricsgenius pydub numpy ffmpeg-python Pillow moviepy
```

Set the Genius token in the environment:

```bash
export GENIUS_ACCESS_TOKEN="your-token"
```

Never commit the token or include it in logs.

## Running the prototype

Run the interactive end-to-end flow with:

```bash
python full_script.py
```

The script asks for a Spotify track URL and the starting offset of the extract.

Additional modes currently available:

```bash
python full_script.py --process_videos
python full_script.py --create_lyrics_video
python full_script.py --delete
```

## Current portability limitations

The checked-in version reflects the original development environment:

- several generated-file paths still reference `/home/kathiou/sp3`;
- the alignment command assumes a specific `NUSAutoLyrixAlign` directory layout;
- the alignment process is launched through a shell command;
- dependency versions are not pinned;
- intermediate filenames are fixed and concurrent runs are not supported;
- cleanup operates on generated files in the current working directory;
- no automated test suite is currently included.

These constraints must be removed before the project can be considered reproducible outside its original environment.

## Engineering focus

The project demonstrates orchestration of heterogeneous tools, subprocess management, external API integration, time-series transformation and media rendering. A production-oriented refactor would separate the pipeline into isolated stages, introduce a workspace per job, validate all subprocess inputs and add deterministic tests around timestamp transformations.

## Responsible use

Only process media and lyrics that you are authorized to download, transform and publish. Third-party services remain subject to their own terms of use and copyright restrictions.

