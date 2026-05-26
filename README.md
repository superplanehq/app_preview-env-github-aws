# Preview Environments — GitHub + AWS

[![Launch in SuperPlane](http://superplane.com/badges/launch-in-superplane.svg)](http://app.superplane.com/install?repo=github.com/superplanehq/app_preview-env-aws)

Spin up a live preview environment for every pull request. Comment `/start` on a PR, get a running app in ~2 minutes. Close the PR, environment auto-destroys.

Built with [SuperPlane](https://superplane.com).

## How it works

1. Open a PR — bot posts a welcome comment with instructions
2. Comment `/start` — SuperPlane creates a AWS EC2, deploys your app via SSH, and posts the preview URL
3. Comment `/destroy` — tears everything down
4. Close/merge the PR — environment auto-destroys
5. Forgot to clean up? A daily TTL check reaps environments older than 72 hours

GitHub Deployments are created along the way, so you get the native "View deployment" button and status badges on the PR.

## Prerequisites

- [SuperPlane](https://superplane.com) account
- **GitHub** integration connected (with Deployments permission)
- **AWS** integration connected
- An SSH key added to both AWS and SuperPlane (as a secret)
- A setup script at `scripts/preview-setup.sh` in your repo

## Quick start

1. Connect GitHub and AWS integrations in SuperPlane
2. Create an SSH key secret in SuperPlane
3. Add a `scripts/preview-setup.sh` to your repo (installs deps, starts your app)
4. Import the canvas:

```bash
superplane canvases create --file canvas.yaml
```

5. Update the integration IDs, repo name, SSH key reference, and setup script URL to match your setup

## Customization

| Setting | Default |
|---|---|
| EC2 instance type | `t3.micro` |
| Region | `nyc1` |
| Image | `ubuntu-24-04-x64` |
| TTL | 72 hours |
| Command | `/start` and `/destroy` |

## License

MIT
