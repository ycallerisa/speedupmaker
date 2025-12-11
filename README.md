# SpeedupMaker – Spotify Lyrics Video Generator

SpeedupMaker is a Python tool that turns a Spotify track URL into a short TikTok-style vertical video with:
- Sped-up audio
- Word-level synchronized lyrics
- Dynamic per-word highlighting over a background video

The project automates an end-to-end media pipeline: audio download, lyrics retrieval, alignment, timing transformation, and final 1080×1920 rendering.

------------------------------------------------------------

## Features

- Download a track from a Spotify URL using spotdl
- Fetch and clean lyrics from Genius via the lyricsgenius API
- Run forced alignment in a Singularity/Kaldi container
- Convert alignment output into word-level JSON timestamps
- Rebuild sentences from word-level alignments
- Speed up audio and adjust all lyrics timestamps
- Render a 1080×1920 vertical TikTok-style lyrics video with highlighted words

------------------------------------------------------------

## Architecture Overview

The pipeline:

1. Input
   - Spotify track URL
   - Background videos stored in: video_background/

2. Audio & Lyrics
   - Download audio via spotdl (song.mp3)
   - Fetch lyrics from Genius and save to: lyrics/scrapedlyrics.txt

3. Alignment
   - Run alignment inside a Singularity/Kaldi container (RunAlignment.sh)
   - Convert output to: lyrics.json
   - Build: sentence_based_lyrics.json

4. Speedup
   - Speed up audio by a configurable factor
   - Update timestamps in the lyrics JSON
   - Trim the audio to the selected duration

5. Rendering
   - Preprocess background videos (scale, fade, concatenate)
   - Render a 1080×1920 TikTok-style layout
   - Export the final output as: final_output.mp4

------------------------------------------------------------

## Requirements

### System Dependencies

- Python 3.10 or newer
- ffmpeg installed on PATH
- spotdl installed globally
- Singularity (or Apptainer)
- Kaldi alignment image (kaldi.simg)

### Python Packages

Install dependencies using:

pip install pydub moviepy pillow numpy ffmpeg-python lyricsgenius

You will also need transitive dependencies from spotdl and lyricsgenius.

------------------------------------------------------------

## Configuration

### Genius API Token

Set your Genius token using:

export GENIUS_ACCESS_TOKEN="your_token_here"

### Background Videos

Place .mp4 background clips in:

./video_background/

These will be scaled, faded, concatenated, and adapted to 1080×1920.

### Fonts

Update the FONT_PATH constant in the code to point to your chosen .ttf font.

------------------------------------------------------------

## Usage

### Run the Full Pipeline

python full_script.py

You will be prompted for:
- A Spotify URL
- A start time in seconds

### Process Background Videos Only

python full_script.py --process_videos

### Generate Only the Final Lyrics Video

If intermediate files already exist (lyrics_speed_up.json, out.mp4, song_speed_up.wav):

python full_script.py --create_lyrics_video

### Cleanup Temporary Files

python full_script.py --delete

------------------------------------------------------------

## Project Structure

```.
├── full_script.py
├── lyrics_fetcher.py
├── video_background/
├── lyrics/
└── final_output.mp4
```

------------------------------------------------------------

## Limitations & Future Improvements

- External dependencies (spotdl, ffmpeg, Singularity, Kaldi) must be installed manually
- Some configuration values (paths, fonts, speed factor) are hard-coded
- No automated tests at this stage
- Not yet packaged as a pip-installable CLI tool

------------------------------------------------------------
