# myNoise for Music Assistant

A community [Music Assistant](https://music-assistant.io) provider that streams the noise
machines of [myNoise](https://mynoise.net) as endless, looping soundscapes.

myNoise machines are interactive soundscapes built from a set of short, seamlessly loopable
audio samples, one per frequency band, mixed by the website. This provider recreates that
model server-side: the layer files are fetched from the myNoise CDN and looped and mixed on
the fly by ffmpeg, so a machine plays forever, just like in the browser.

## What it offers

- **Browse** the myNoise catalog by category and **search** it by name; every machine plays as
  a radio item that never ends.
- The band mix of each machine is played as designed by its author; the loudness of the whole
  mix can be adjusted in the settings.
- The catalog and each machine's layer files are cached, so browsing works offline after the
  first load and playback does not hammer the site.

## Requirements

myNoise grants access to its machines — the free ones included — to donors, validated by the
email address of a donation (no password). **This provider requires that access**: setup asks
for the email address of your myNoise donation and verifies it with myNoise before the
provider is created, and no machine is offered until myNoise has accepted it.

Please [support myNoise and donate](https://mynoise.net/donate.php) if you have not already.

## Setting it up

1. In Music Assistant, open **Settings > Music sources** and add **myNoise**.
2. Enter the email address of your myNoise donation. Setup only finishes once myNoise accepts
  it; a rejected address simply asks again.

### Options

| Setting | What it does |
|---|---|
| Patron email | The donation email myNoise accepted; changing it retries the verification. |
| Master gain (dB) | Extra gain applied to the final mix of every machine (start with -6 if machines play too loud). |
| Use alternate sample takes | myNoise ships two takes of every layer and the website picks one at random; enable to always use the B-variant. |
| Refresh machine catalog | Re-download the list of machines from mynoise.net (normally refreshed every 30 days); also retries the patron login, so a corrected email takes effect here. |

## How it works

myNoise has no official API. The catalog is scraped from the public index page (a bundled
fallback covers the first start without internet) and each machine's layer files are parsed
from the config object embedded in its generator page; both are cached aggressively.

During playback the selected layer files are staged in a private temporary directory inside
the server's config folder, so the endless ffmpeg mix replays local data instead of
re-fetching from the CDN on every pass. The directory is deleted again when playback stops.
Output is 44.1 kHz 16-bit stereo PCM.

myNoise content is (c) Stephane Pigeon / myNoise BV, "FOR PERSONAL USE ONLY". This provider
streams the same public files the website serves and is intended for personal use.

## Developing

You need [uv](https://docs.astral.sh/uv/) (and Docker with the Dev Containers extension of
VS Code, which sets all of this up for you: **Reopen in Container**).

```sh
uv sync              # install the server and the tools
uv run pytest        # checks the manifest, texts and icon, and boots a server that loads the provider
uv run ruff check .  # lint
uv run mypy          # types
uv run codespell     # spelling
```

Run a development server that loads this provider with the task **Run Music Assistant** or
`scripts/run.sh`, then open <http://localhost:8095> and add myNoise under Settings > Music
sources. Settings and library stay in `.data`; delete that folder to start over.

## Licence

Apache-2.0 for this provider's code, like Music Assistant. The myNoise content it streams
remains (c) Stephane Pigeon / myNoise BV.
