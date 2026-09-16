**English** | [简体中文](README.md)

# play-console-data-safety

A Claude Code skill: fill out Google Play Console's Data safety
questionnaire (5 steps). Not part of Store listing, yet an "Incomplete"
status here disables the "Submit changes for review" button on
Publishing overview. Generic skill, not tied to any specific project
directory. Body text is Chinese-first in the shipped SKILL.md content
(per this author's default); this top-level README is English for
discoverability. Code, CLI commands, file paths, and technical terms are
kept verbatim either way.

## Install

```
/plugin marketplace add mosjin/play-console-data-safety-skill
/plugin install datapub@play-console-data-safety
```

Invoke with the short name `datapub` (e.g. `/datapub:version`,
`/datapub:upgrade`), following the same convention as
`mosjin/android-voice-test-kit`: a descriptive marketplace/repo name
paired with a short plugin command name.

## What this covers

- The 5-step Data safety questionnaire, especially the "Data collection
  and security" step's two easily-confused URL fields — "Delete account
  URL" (deletes the whole account) vs. "Delete data URL" (deletes some
  data without deleting the account) — missing the former keeps "Next"
  disabled while you keep re-checking the latter, which is already
  correctly filled.
- Filling the Data types step is a factual legal disclosure, not an
  automation task — verify the project's actual data collection and
  third-party SDK behavior first (voice apps in particular usually don't
  qualify for the "ephemeral processing" exemption, since audio really
  does get sent to a third-party ASR service).
- A reference mapping table from common project data (account email/
  name, voice recordings, user-generated content, purchase tokens,
  device location) to Google's Data types categories.
- Shared with `assetpub`/`signinpub`: the form is "not saved until you
  finish the questionnaire" — reopening the page URL silently discards
  unsaved input.

## Related

- [`play-console-signin-skill`](https://github.com/mosjin/play-console-signin-skill)
  (signinpub) — the other half of the same Publishing overview
  pre-submission declarations, Sign in details.
- [`play-console-assets-skill`](https://github.com/mosjin/play-console-assets-skill)
  (assetpub) — another stage of the same Play Console publishing flow,
  covering store listing graphics upload.
- [`play-console-app-publish`](https://github.com/mosjin/play-console-app-publish-skill)
  (playpub) — the Chrome CDP connection method, app creation, Internal
  testing uploads, subscription product creation.
