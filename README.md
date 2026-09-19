# Spotify Mini Player for Hammerspoon

A compact Spotify controller for macOS. It shows track artwork, title, artist, playback progress, and media controls in a Hammerspoon canvas.

The player:

- Appears while Spotify runs.
- Hides when Spotify's native mini player exists.
- Fits its width to the title or artist.
- Animates progress, artwork changes, and width changes.
- Supports built-in and external display placement.
- Uses Spotify's local AppleScript interface. It needs no API key or OAuth token.

## Requirements

- macOS
- [Hammerspoon](https://www.hammerspoon.org/)
- The Spotify desktop app

Allow Hammerspoon to use Accessibility and control Spotify when macOS asks. Accessibility supports the Like shortcut. Automation supports playback commands and track details.

## Install

Clone the repository and link the module into your Hammerspoon configuration:

```sh
git clone https://github.com/svintit/spotify-miniplayer.git ~/src/spotify-miniplayer
ln -s ~/src/spotify-miniplayer/spotify_miniplayer.lua ~/.hammerspoon/spotify_miniplayer.lua
```

Add this setup to `~/.hammerspoon/init.lua`:

```lua
_G.spotifyMiniPlayer = require("spotify_miniplayer").start({
    preferredScreens = {},
    fallbackScreen = "Built-in Retina Display",
    minWidth = 320,
    maxWidth = 650,
    height = 52,
    padding = 9,
    builtInInset = 50,
})
```

Reload Hammerspoon.

Set `preferredScreens` to external display names in priority order. Hammerspoon uses the first connected match. When `fallbackScreen` matches, the player uses the full screen frame. This places `padding` from the physical screen top instead of the menu bar bottom.

Get connected display names from the Hammerspoon console:

```lua
hs.inspect(hs.fnutils.imap(hs.screen.allScreens(), function(screen) return screen:name() end))
```

## Controls

| Control | Action |
|---|---|
| Artwork and track details | Open Spotify |
| Back | Previous track |
| Play or pause | Toggle playback |
| Forward | Next track |
| Shuffle | Toggle shuffle when Spotify supports it |
| Heart | Send Spotify's Option-Shift-B Like or Unlike shortcut |

The heart does not read saved-track state. It stays outlined and opens Spotify before it sends the shortcut.

## Configuration

| Option | Default | Purpose |
|---|---:|---|
| `preferredScreens` | `{}` | External display names in priority order |
| `fallbackScreen` | `nil` | Screen that uses built-in placement |
| `minWidth` | `320` | Minimum player width in pixels |
| `maxWidth` | `650` | Maximum player width in pixels |
| `height` | `52` | Player height in pixels |
| `padding` | `9` | Distance from the selected screen top |
| `builtInInset` | `50` | Horizontal inset for built-in placement |

Without a matching preferred or fallback screen, the player centers on the primary screen below its menu bar.

## Controller methods

`start()` returns a controller:

```lua
local player = require("spotify_miniplayer").start(options)

player:refresh()
player:status()
player:snapshot("/tmp/spotify-mini-player.png")
player:stop()
```

`snapshot(path)` writes to the path supplied by your local Hammerspoon configuration.

## Privacy and network use

The module stores no credentials and sends no analytics. It reads playback details from the local Spotify app. It downloads only HTTPS artwork URLs reported by Spotify.
