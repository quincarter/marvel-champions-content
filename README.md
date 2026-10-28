# Marvel Champions: Digital Edition, media bundles

This repository holds the card scans, music and art of
[marvel-champions-digital-game](https://github.com/quincarter/marvel-champions-digital-game). **The media is not in
this repository's files: it is in its [Releases](https://github.com/quincarter/marvel-champions-content/releases).**
Each release holds one zip file, a *bundle*, and nothing else lives here but this README.

## How it fits together

- **Bundles.** Media is grouped into bundles, each a zip of one folder of the game repo:
  - `card-art-<cycle>`: English card scans, one bundle per cycle. A scan is named by its card code, and the code's
    first two digits are its pack, so `card-art-aoa` holds the scans starting `45` to `49`.
  - `card-art-es`: the Spanish scans.
  - `music-<folder>`: one per folder of `music/` (`soundtrack`, `scenarios`, `campaigns`, ...).
  - `art-<folder>`: one per folder of `art/` (`heroes`, `campaigns`, `title`, ...).
  - `artifacts`: QA and design screenshots, kept for reference and never shipped in the game.
- **Releases.** Every version of a bundle is its own release, tagged `<bundle>-<first 12 hex of its sha256>`, for
  example `card-art-aoa-605b939e9c33`. Releases are never edited or deleted, so every commit of the game can still
  download exactly the media it was built with.
- **The manifest.** The game repo's
  [`assets/manifest.json`](https://github.com/quincarter/marvel-champions-digital-game/blob/main/assets/manifest.json)
  lists every bundle: the folder it unpacks to, its release tag, file name, size and sha256. Changing a bundle means a
  new release here plus a one-line change to that manifest.
- **Fetching.** In the game repo, `pnpm assets:fetch` downloads the bundles the manifest names, checks each sha256
  and unpacks them where the game expects them (`assets/`, `music/`, `art/`). It keeps one cache per machine, shared by
  every checkout, and skips bundles already installed. CI and the release builds do the same.

## Contributing images or music

You don't need access to this repository. Everything goes through a pull request to the game repo:

1. In your fork or clone of
   [marvel-champions-digital-game](https://github.com/quincarter/marvel-champions-digital-game), run `pnpm install` and
   `pnpm assets:fetch`.
2. Put each new or replacement file in the game repo's `incoming/` folder, at the path it belongs at, keeping its name.
   For example:
   - a card scan: `incoming/assets/card-art/bundles/cards/45001a.png`
   - a Spanish scan: `incoming/assets/card-art-locales/es/bundles/cards/45001a.png`
   - a scenario's battle track: `incoming/music/scenarios/rhino/battle.mp3`
   - a hero portrait: `incoming/art/heroes/<folder>/<file>.png`

   Leave `incoming/.gitkeep` in place.
3. Run `pnpm assets:fetch` again to see your files in the game (`pnpm dev`). `node scripts/assets-incoming.mjs` shows
   which bundle each file joins.
4. Commit the files in `incoming/` and open a pull request. The "Incoming media" check confirms every file has a
   bundle to go to.
5. After the merge, a workflow in the game repo uploads the updated bundles here as new releases and opens a pull
   request that updates the manifest and empties `incoming/`.

The full guide is in the game repo's
[CONTRIBUTING.md](https://github.com/quincarter/marvel-champions-digital-game/blob/main/CONTRIBUTING.md#contributing-images-and-music).

## Maintainers

`pnpm assets:pack <bundle>` in the game repo zips a bundle from the files on disk, uploads it here with `gh` and
rewrites its manifest line (`pnpm assets:pack --all --dry-run` shows what changed). The game repo's "Incoming media"
workflow uploads with the `CONTENT_REPO_TOKEN` secret, a fine-grained token limited to this repository with Contents
read and write.

Marvel Champions, its card text, artwork and characters are the property of Fantasy Flight Games and Marvel. This is a
fan-made, non-commercial project.
