# selfhost branch

This fork's `selfhost` branch is upstream Vexa at a release tag plus a short, curated stack of
local commits (one commit per change). `main` stays a plain mirror of `Vexa-ai/vexa` main.

## Local changes

- **Terminal room count.** The meeting header's "N in the room" counts distinct transcript
  speakers plus known participants (upstream hard-codes an empty participant list, so it always
  read 0). The meeting tab and its canvas share one live meeting subscription.
- **Authenticated bots drop `--incognito`.** The join lane's browser args carry `--incognito`,
  which ignores the restored Google profile's cookies, so an authenticated bot reached the Meet
  lobby signed out.
- **Image (`Dockerfile.lite-patched`).** Published Lite image + this fork's terminal and bot,
  plus the aws CLI the authenticated-bot session store shells out to (Lite does not ship it).

## Build

```sh
docker build -f deploy/selfhost/Dockerfile.lite-patched --build-arg VEXA_VERSION=v0.12.28 -t vexa-lite-patched .
```

## Taking an upstream release

Rebase the stack onto the new tag (keep it one commit per change; squash fixups):

```sh
git remote add upstream https://github.com/Vexa-ai/vexa.git   # once
git fetch upstream --tags
git rebase --onto v0.12.29 v0.12.28 selfhost   # old tag → new tag
# resolve conflicts; drop commits upstream made redundant; bump VEXA_VERSION in the Dockerfile
git push --force-with-lease origin selfhost
gh repo sync geek0609/vexa -b main             # keep the mirror branch current
```
