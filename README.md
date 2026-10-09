# digestabot-examples

This repository contains examples of Github Actions workflows that extend the
functionality of `digestabot` for different use cases.

You can see the workflows in [`./.github/workflows`](.github/workflows).

The workflows create Pull Requests in this repository.

## Digestabot

Demonstrates the straightforward usage of `digestabot`.

## Chainctl Image Diff

Runs `chainctl image diff` on the updates made by `digestabot`.

It includes a summary of the differences in the PR body.

It also avoids making changes that do not resolve any vulnerabilities. This
could prevent some of the toil of reviewing PRs for images that are frequently
updated.

## Grype Scan

Runs `grype` on each image update and adds a comment to the PR with the scan
results.

It also registers a failed check against the commit for any `High` or
`Critical` severity CVEs.

## Verify Signatures

Verifies the `cosign` signature of every new digest, against the policy in
[`.github/digestabot-signatures.yaml`](.github/digestabot-signatures.yaml),
before writing it to a file. An update that doesn't verify is skipped and the
old digest is kept, so an unverified image never reaches the PR.

It also fails the workflow when an update is skipped, which `digestabot` does
not do on its own.

## Stale Tags

Warns about tags that haven't been rebuilt in 30 days. Such a tag has usually
reached its end of life, so the image it points at stops receiving patches
while `digestabot` goes on reporting no changes.

It keeps a single issue in sync with the stale tags, since moving to a
supported tag outlives any one digest PR, and closes it once they are current
again.

`job.yaml` pins `docker.io/library/python:3.8-slim` so this workflow has
something to find. Python 3.8 went end of life in 2024 and the tag stopped
being rebuilt, which is the whole point: its digest never moves, so every
other workflow here reports nothing to do while the image goes on ageing.
The `cgr.dev/chainguard/python` tag beside it is rebuilt daily and is never
flagged.

## SBOM Diff

Diffs the SPDX SBOM attestation of each updated image between the old and the
new digest, so the PR says which packages changed rather than just which
digest did.

The package changes are added as a comment on the PR, alongside a link to the
artifact with the full diff.

## Cooldown

Only updates to digests that are at least 7 days old, so a
[cooldown policy](https://edu.chainguard.dev/chainguard/chainguard-repository/container-policies/#cooldown)
on the registry doesn't block pulling the digest that `digestabot` just
proposed.

> [!NOTE]
> The four workflows above use features that aren't in a `digestabot` release
> yet, so they pin `main`. Move them to a release tag once one includes them.
