# yt-grab-sign

Source of truth for the "YT Grab" Apple Shortcut (a yt-dlp client that POSTs
to `https://yt.fede.one/v1/api`; `https://fede.one/yt-dlp/api/shortcut` is the
same handler on the old host and also works).

> **Status (2026-07): the GitHub Actions signing pipeline below DOES NOT
> work.** `shortcuts sign` requires the Mac to be signed into iCloud, which is
> impossible on ephemeral GitHub-hosted runners (verified empirically — see
> the failed runs). The working path is **rebuilding the shortcut by hand
> on-device**, which needs no signing at all:
>
> - `BUILD-BY-HAND.txt` — tap-by-tap build guide (English)
> - `BUILD-BY-HAND-IT.txt` — the same guide in Italian
> - `SIGNING-STEPS.txt` — fallback: how to sign the draft XML on a rented
>   cloud Mac (Scaleway), if a distributable `.shortcut` file is ever needed
>
> `drafts/YT Grab.xml` remains the canonical description of the shortcut's
> logic (actions, menus, JSON bodies) and is kept in sync with the API.

## How to sign a new build

1. Go to the **Actions** tab on this repo.
2. Select the **Sign YT Grab Shortcut** workflow.
3. Click **Run workflow** (top right) → **Run workflow** again to confirm.
4. Wait for the run to finish (usually under a minute).
5. Open the completed run, scroll to **Artifacts**, and download `yt-grab-shortcut`.
6. Unzip it — inside is `YT Grab.shortcut`, ready to AirDrop / iCloud-share /
   host on fede.one and open on iPhone.

## Updating the shortcut logic

The shortcut itself is built and maintained elsewhere (Claude Code's
shortcuts-playground agent). When the logic changes:

1. Re-export/copy the updated `YT Grab.xml` from the shortcuts-playground
   drafts output.
2. Replace `drafts/YT Grab.xml` in this repo with the new version (same
   filename — the workflow references that exact path).
3. Commit and push.
4. Re-run the workflow as above to get a freshly signed `.shortcut`.

## Repo structure

```
yt-grab-sign/
├── .github/workflows/sign.yml   # workflow_dispatch-only signing job
├── drafts/YT Grab.xml           # unsigned source (input to the signer)
└── README.md
```

## Notes

- The workflow only runs manually (`workflow_dispatch`) — it never triggers
  on push, so editing the draft doesn't burn Actions minutes by itself.
- This repo is currently **public** to get unlimited free macOS Actions
  minutes. If it gets flipped to private for a while, macOS runner minutes
  are billed at a much steeper multiplier than Linux minutes, so flip it back
  to public before running the workflow repeatedly.
- The workflow fails loudly (non-zero exit) if the draft XML is missing or if
  `shortcuts sign` errors, so a broken run never produces a silently-empty
  artifact.
