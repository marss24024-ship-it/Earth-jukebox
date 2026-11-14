# Earth Jukebox

Turn pictures of our changing planet into music. Pick an Earth visualization, press play, and a playhead reads the image from left to right. The pixels it passes decide which notes sound.

This project has two parts that use the same rules:

- **Web jukebox** (`index.html`): an interactive page that runs entirely in the browser.
- **Audio API** (`earth-jukebox-api/`): a Python backend that turns any image into a WAV file.

## Try the web jukebox

Open `index.html` in a browser, or visit the live site: `https://YOUR-USERNAME.github.io/earth-jukebox/`

1. Pick a track on the left.
2. Press **Play**.
3. Drag the **Year** slider, or tick **Time-lapse**, to hear the data change over 2000 to 2025.
4. Use **Load your own frame** to play any image, such as a real NASA EIC frame.

The built-in frames are stylized stand-ins drawn in the browser, shaped like EIC datasets. They are not NASA data.

## How a frame becomes sound

The image is split into 64 time steps (left to right) and 8 latitude bands (top to bottom).

| Image feature | Sound |
|---|---|
| Height in the frame | Pitch. Higher bands play higher notes in the track's scale. |
| Brightness | Which notes sound (bright, changing regions only) and how loud. |
| Colour saturation | Timbre. Vivid colour is bright and sharp, washed-out colour is soft. |
| Left-to-right position | Time, and stereo pan. |
| Average brightness of the whole frame | Tempo (58 to 104 BPM) and the level of the low drone. |

## The six tracks

| Track | Mode | Sound |
|---|---|---|
| Sea surface temperature | Lydian | Open sine pads |
| Arctic sea ice | Minor pentatonic | Glassy bell tones |
| Atmospheric carbon dioxide | Phrygian | Buzzing sawtooth, pulses faster |
| Wildfire hot spots | Hirajoshi | Crackling percussion |
| Vegetation greenness | Major pentatonic | Bright plucks |
| Sea level anomaly | Dorian | Slow, low swells |

## Run the audio API

Requires Python 3.10 or newer.

```
cd earth-jukebox-api
pip install -r requirements.txt
uvicorn app:app --reload
```

Open http://localhost:8000/docs to try the endpoints in your browser.

| Endpoint | Purpose |
|---|---|
| `POST /sonify` | Send an image, `preset` (sst, ice, co2, fire, veg, sealevel) and `loops` (1 to 3). Returns a WAV. |
| `POST /analyze` | Returns the tempo and energy found in the image, without audio. |
| `GET /presets` | Lists the presets. |
| `GET /health` | Checks the server is running. |

Example:

```
curl -X POST localhost:8000/sonify -F "file=@frame.png" -F "preset=ice" -o ice.wav
```

### Make a playlist of audio files

```
cd earth-jukebox-api
python make_playlist.py frame.png
```

This writes one WAV per preset into a `playlist` folder.

## Project layout

```
index.html              web jukebox (HTML, CSS, JavaScript, Web Audio API)
earth-jukebox-api/
  sonify.py             image analysis and sound synthesis (numpy, Pillow)
  app.py                FastAPI server
  make_playlist.py      batch audio generation
  requirements.txt
```

## Deploy

- **Web jukebox:** upload `index.html` to a GitHub repository and turn on GitHub Pages (Settings, then Pages, branch `main`, folder `/ (root)`).
- **API:** host on any service that runs Python, such as Render or Railway. Start command: `uvicorn app:app --host 0.0.0.0 --port $PORT`.

## Limitations and next steps

- The web page and the API are not connected yet. A "Render with server" button could post the image to `/sonify`.
- Frames from NASA's Earth Information Center are loaded by hand. Fetching them automatically would need a backend job.
- The brightness-to-pitch mapping is the same for every image. Per-dataset tuning, such as using a colour bar's real values, would make the music more faithful to the data.

## Credits

Built for the NASA Earth Information Center "Earth Jukebox" challenge.
