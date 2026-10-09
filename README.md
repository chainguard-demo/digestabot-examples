# digestabot-examples

This repository contains examples of Github Actions workflows that extend the
functionality of `digestabot` for different use cases.

You can see the workflows in [`./.github/workflows`](.github/workflows).

The workflows create Pull Requests in this repository.

## Tokens

The workflows don't use `GITHUB_TOKEN`. GitHub Actions isn't allowed to create
pull requests here, so every workflow used to do its work and then fail on the
last step with *"GitHub Actions is not permitted to create or approve pull
requests"*.

Each one instead mints a short-lived token with
[octo-sts](https://github.com/octo-sts/app), from the trust policy of the same
name in [`./.github/chainguard`](.github/chainguard). A policy names the
workflow allowed to assume it and the permissions it gets, so a workflow can
only ever mint the access it actually needs.

This requires the Octo STS app to be installed on the organization, with
access to this repository. The policies pin `refs/heads/main`, so they have to
be merged before a workflow can use them.

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
