# Dusk release website

This is a static website for publishing Dusk ISO releases. It needs no package
installation or build step. The current release page reads `releases.json`,
which the Dusk Settings screen can check for newer ISO releases.

## Preview locally

From this directory, run:

```sh
python3 -m http.server 8000
```

Open `http://localhost:8000` in a browser. Use a web server rather than opening
`index.html` directly, since the page fetches `releases.json`.

## Publish a new ISO

1. Copy the new ISO into `downloads/`.
2. Calculate its SHA-256 checksum and exact byte size. On Linux, run
   `sha256sum downloads/your-release.iso` and `stat -c %s downloads/your-release.iso`.
3. Add a new entry at the beginning of `releases.json`. Keep the field names
   and types consistent with the existing entry. Use a download path relative
   to this website, and put the correct checksum and byte size in the entry.
4. Preview the page, verify that the download works, and independently compare
   the displayed checksum with the checksum of the ISO.
5. Upload the website files and `releases.json` to a static website host.
   For GitHub, use GitHub Pages for the site and attach the ISO to a GitHub
   Release; do not commit the ISO into the repository.

The latest entry must stay first in `releases.json`. Older entries are shown in
the release history. Do not replace an ISO while leaving an old checksum in
the metadata.

## Publish with GitHub Pages and Releases

The Dusk ISO is larger than GitHub's 100 MiB regular repository file limit,
but is below GitHub's 2 GiB per-release-asset limit.

1. Create a public GitHub repository. Upload `index.html`, `releases.json`,
   and optionally this README to its root. Do not upload the `downloads/`
   folder or commit the ISO.
2. Create a GitHub Release with a tag such as `v2026.11.21.1`, and attach
   `Dusk-release-checker.iso` from the local `downloads/` folder.
3. Copy the download URL for that release asset. Edit `releases.json` in the
   repository and change the newest release's `file` value to that full HTTPS
   URL, for example
   `https://github.com/OWNER/REPOSITORY/releases/download/v2026.11.21.1/Dusk-release-checker.iso`.
4. In the repository, open **Settings → Pages** and publish the `main` branch
   from the root folder. The website URL will be
   `https://OWNER.github.io/REPOSITORY/`.
5. For Dusk's Settings checker, use the public raw catalog URL:
   `https://raw.githubusercontent.com/OWNER/REPOSITORY/main/releases.json`.

Replace `OWNER` and `REPOSITORY` with the actual GitHub account and repository
names. After publishing a later version, upload its ISO as a new GitHub Release
asset, update `releases.json` with the new asset URL and checksum, then commit
the updated catalog. GitHub Pages will publish the catalog change.

## Connect Dusk to this release catalog

After publishing the site over HTTPS, copy the public URL ending in
`releases.json`. In Dusk Settings → OS updates, paste the URL, choose **Save
URL**, then **Check for updates**. The web host must allow cross-origin (CORS)
reads from the Dusk screen. If a newer version is listed, choose its download
button to save the ISO in Chromium's Downloads folder.

With the GitHub setup above, use the `raw.githubusercontent.com` URL for the
catalog in Dusk.

The update checker is included starting with the Dusk ISO version listed in
`releases.json`. Older Dusk images do not contain this feature. This is a live
ISO download checker—not an OS installer. It cannot replace the running ISO or
restart the VM for you. Back up settings and boot from the downloaded ISO
manually.
