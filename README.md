# yt-grab-sign

Mac-free signing pipeline for the "YT Grab" Apple Shortcut. iOS 15+ requires
shortcuts to be cryptographically signed, and the only tool that can do that
(`shortcuts sign`) only exists on macOS. This repo uses a free GitHub Actions
macOS runner to sign the shortcut on demand, so importing it on iPhone doesn't
throw a "can't verify" error.

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
