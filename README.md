# Preview Environments on AWS

[![Launch in SuperPlane](https://superplane.com/badges/launch-in-superplane.svg)](https://app.superplane.com/install?repo=github.com/superplanehq/app_preview-env-github-aws)

A SuperPlane app that spins up preview environments on AWS EC2 for GitHub pull requests. Comment `/start` on a PR, get a running app on a fresh instance in ~2 minutes. Close the PR, environment auto-destroys.

## How it works

1. Open a PR — the bot posts a welcome comment with instructions
2. Comment `/start` — creates an EC2 instance, deploys your app via SSH, runs a health check, and posts the preview URL
3. Comment `/destroy` — tears everything down
4. Close or merge the PR — environment auto-destroys
5. A scheduled TTL check cleans up environments older than 24 hours

GitHub Deployments are created for each environment, so you get the native "View deployment" button and status badges on the PR.

## Prerequisites

- A [SuperPlane](https://superplane.com) account with GitHub and AWS integrations connected
- An EC2 key pair registered in AWS
- The corresponding private key stored as a SuperPlane secret (key name: `private-key`)

## Install

Click **Launch in SuperPlane** at the top of this page. The wizard will walk you through connecting integrations, selecting your repository, picking an SSH secret, and entering your EC2 key pair name.

## Customizing the setup script

The app includes `scripts/preview-setup.sh` in the **Files** tab. This script runs on each new instance to install dependencies and start your application.

The default script sets up a Node.js app with nginx. Edit it to match your own stack — different runtime, build steps, service configuration.

The script receives these environment variables from the workflow:

- `PR_NUMBER` — the pull request number
- `REPO_URL` — the full clone URL of the repository

## License

MIT
