# Score Vinyl

Film, TV, game and stage scores on Spotify, in a keyboard-driven player window
that lives in the Omarchy shell and wears your theme.

It's [Spotify Vinyl](https://github.com/funcoder/omarchy-spotify) narrowed to
scores: the same spinning record, tonearm, theme-duotone artwork and cava
visualizer, with browsing built around composers and scores, not the whole
catalog.

- **Composers.** 45 film, TV, game and musical-theatre composers. Enter opens their
  scores, newest first, and Enter again opens a score's cues. Shift+Enter plays.
- **Classics.** Landmark scores, from *The Godfather* to *Dune: Part Two*,
  grouped by decade.
- **New.** Scores released in the last fortnight, with the ones by composers on
  the list first.
- **My scores.** The scores among your saved albums. `Shift+L` on any score
  saves it here.
- **Search** only finds scores: score albums, composers, cues from scores and
  playlists about film, TV and game music.
- **Playlists** shows your score playlists. The rest are folded away under "Show N
  other playlists", and `x` moves any playlist from one group to the other.
- Also: the **Queue**, and **Devices** on `d` (Spotify Connect).

## How "only scores" works

Spotify's API no longer says which genre an album is, so Score Vinyl goes by the
title. Labels name scores very consistently: "(Original Motion Picture Score)",
"Music from the HBO Series", "Original Video Game Soundtrack", "Original
Broadway Cast Recording". A song-heavy compilation titled "Soundtrack" counts
too.

A playlist you own or collaborate on counts as a score playlist when more than a
third of its tracks (from the first 100) come from scores. A party playlist with
one movie song stays out. Spotify doesn't let development-mode apps read the
tracks of playlists you only follow, so those go by name and description:
"score", "soundtrack", "film", a 'From "Film"' title and so on. Press `x` to
move any playlist the rules get wrong; your choices are saved in `config.json`.
Track checks are cached until a playlist changes, so they're only redone then.

The Composers and Classics lists ship with the plugin in `catalog.json`, so browsing
them costs no Spotify API calls. Add your own entries there by Spotify ID.

## Requirements

- [Omarchy](https://omarchy.org) 4.
- Spotify **Premium**, and your own free Spotify developer app (see Setup).
- `spotifyd` to play on this computer, and `cava` for the visualizer:

  ```sh
  sudo pacman -S spotifyd cava
  ```

## Install

```sh
omarchy plugin add https://github.com/funcoder/omarchy-scores.git --enable
```

Then bind a key in `~/.config/hypr/bindings.lua` and run `hyprctl reload`:

```lua
o.bind("SUPER + SHIFT + F", "Score Vinyl", "omarchy-shell shell toggle funcoder.scores '{}'")
```

## Setup

If you use Spotify Vinyl (or cliamp), Score Vinyl finds its Spotify app's
client ID, so you only have to log in. Otherwise:

1. Open <https://developer.spotify.com/dashboard> and choose **Create app**.
   Redirect URI: `http://127.0.0.1:19872/login`. Tick **Web API**.
2. Open Score Vinyl, paste the **Client ID**, press Enter, then Enter again to
   log in in your browser.
3. Press `d` for Devices, then Enter on **This computer**.

Score Vinyl and Spotify Vinyl share one spotifyd device (`funcoder-spotifyd`),
so "This computer" is the same in both.

### Sharing Spotify's quota

Both plugins use the same Spotify app, and a development-mode app has a small
request quota. Score Vinyl only checks what's playing while its window or bar
widget is showing. New releases are cached for 6 hours, and each composer's
page for a day.

### Where things are stored

| What | Where |
| --- | --- |
| Settings | `~/.config/funcoder-scores/config.json` |
| Spotify refresh token | Desktop keyring (`secret-tool`, service `funcoder-scores`) |
| Cached composer pages and new releases | `~/.cache/funcoder-scores/` |
| Shared spotifyd setup | `~/.config/funcoder-spotify/spotifyd.conf`, `~/.cache/funcoder-spotify/spotifyd/` |

### Uninstall

```sh
secret-tool clear service funcoder-scores
rm -rf ~/.config/funcoder-scores ~/.cache/funcoder-scores
omarchy plugin remove funcoder.scores
```

(Leave the spotifyd files alone if you still use Spotify Vinyl.)

## Keys

| Key | Action |
| --- | --- |
| `↑↓` / `j k` | Move |
| `Enter` | Open a composer, score or playlist; play a cue |
| `Shift+Enter` | Play without opening |
| `Esc` / `Backspace` | Back (from a score to its composer, then to the list) |
| `Tab` / `1`–`7` | Switch tabs |
| `/` | Search |
| `d` | Devices |
| `Space` | Play or pause |
| `← →` | Seek 10 s (`Shift` for 30 s) |
| `n` / `p` | Next or previous cue |
| `+` / `-` / `m` | Volume up, volume down, mute |
| `s` / `r` | Shuffle, cycle repeat |
| `l` | Like the playing cue |
| `Shift+L` | Like the selected cue, or save the selected score to My scores |
| `a` | Add the selected cue to the queue |
| `x` | In Playlists: move a playlist into or out of your score playlists |
| `c` | Open the score or playlist that's playing |
| `o` | Switch between omarchified and original artwork |

IPC: `omarchy-shell funcoder.scores.player playPause|next|previous|volumeUp|volumeDown|like|status`, and `seek 90` or `seek 50%`.

## Limits

Spotify's development mode returns 10 search results per type and blocks charts,
new-release browsing and artist top tracks. That's why Classics is a bundled
list and New is built from searches. Playlists you don't own play but don't list
their tracks.

## Development

`scores.py <op> '<json>'` runs one helper request from a terminal (for example
`scores.py composer '{"id":"0YC192cP3KPCRWx8zr8MfZ"}'`). Set `"demo": true` in
`~/.config/funcoder-scores/config.json` to try the UI with fake data. After
editing QML, run `omarchy restart shell`.

## Credits

Built on Spotify Vinyl. Playback by [spotifyd](https://github.com/Spotifyd/spotifyd),
visualizer by [cava](https://github.com/karlstav/cava). Not affiliated with Spotify.
