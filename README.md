# portal_xml

Release binaries for the attendance / portal desktop app, served straight from
this repo via raw URLs. `appversion.json` is the update manifest the app reads
to find the current version and download link for each platform.

| File | Platform |
|---|---|
| `attendace_app-<version>-windows-setup.exe` | Windows installer |
| `attendace_app.run` | Linux installer |
| `portalapp.dmg` | macOS disk image |
| `appversion.json` | Update manifest — **do not delete** |

## Uploading a new build

Pushing a 50+ MB binary from a laptop over HTTPS usually fails with
`HTTP 408` / `the remote end hung up unexpectedly`. Instead, let GitHub
download the file itself with the **Import file into main** workflow
(`.github/workflows/import.yml`). It runs on GitHub's servers, fetches the
file from a URL and commits it to `main`.

1. Put the build somewhere reachable by URL — a WeTransfer link
   (`we.tl/…` or `wetransfer.com/downloads/…`, **one file per transfer**) or
   any direct download link.
2. Trigger the download. Replace `<URL>` with your link and pick the `path`
   that matches the platform. `<URL>` may be a WeTransfer link or any direct
   download link.

   ```sh
   # macOS
   gh workflow run import.yml -R alihassan-pixel/portal_xml \
     -f url="<URL>" -f path="portalapp.dmg"

   # Linux
   gh workflow run import.yml -R alihassan-pixel/portal_xml \
     -f url="<URL>" -f path="attendace_app.run"

   # Windows (use the new version in the filename)
   gh workflow run import.yml -R alihassan-pixel/portal_xml \
     -f url="<URL>" -f path="attendace_app-1.0.33+1-windows-setup.exe"
   ```

   `path` is where the file lands in the repo. An existing file at that path is
   replaced; identical bytes are skipped.

3. Trigger and watch it finish in one go (usually well under a minute):

   ```sh
   gh workflow run import.yml -R alihassan-pixel/portal_xml \
     -f url="<URL>" -f path="portalapp.dmg" &&
     sleep 3 && gh run watch -R alihassan-pixel/portal_xml
   ```

4. Delete the previous version's installer if its filename changed (e.g. the
   old `…-windows-setup.exe`), then update `appversion.json` with the new
   version numbers and URLs. Raw download URLs have the form:

   ```
   https://github.com/alihassan-pixel/portal_xml/raw/refs/heads/main/<file>
   ```

The workflow can also be started from the **Actions** tab on GitHub
("Import file into main" → *Run workflow*).

Requires the [GitHub CLI](https://cli.github.com) (`gh auth login` once).

## Keeping a local clone in sync

If you also keep a clone of this repo (e.g. `~/Desktop/files`), pull after
each import so the local branch doesn't diverge:

```sh
git fetch origin && git checkout -B main origin/main
```
