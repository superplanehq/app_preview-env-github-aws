# Agent Guidelines — Preview Environments on AWS

This file provides context for AI agents (SuperPlane built-in agent, Cursor, or any external agent) operating on this canvas.

## What this app does

This app manages the full lifecycle of ephemeral preview environments for GitHub pull requests on AWS EC2:

- **Provision** — create an EC2 instance, deploy the app via SSH, run a health check, post the preview URL
- **Teardown** — terminate the instance and clean up memory on `/destroy` command or PR close
- **TTL** — a scheduled check reaps environments older than 24 hours

## Flows

### Deploy (`/start` command)
```
/start comment → Ack Deploy → Check Exists → Create Instance → Create Deployment
  → Status: In Progress → Setup App (SSH) → Health Check
  → (pass) Save Environment → Post Preview URL → Status: Success
  → (fail) Setup Failed comment → Cleanup Failed Instance → Status: Failure
```

### Destroy (`/destroy` command)
```
/destroy comment → Ack Destroy → Read Env → Delete Instance
  → Destroyed Comment → Cleanup Memory → Status: Inactive
```

### PR Closed
```
PR Closed → Read Env (Close) → Delete Instance (Close)
  → Destroyed Comment (Close) → Cleanup Memory (Close) → Status: Inactive (Close)
```

### TTL Check (scheduled)
```
TTL Check (schedule) → Read All Envs → Older than 24h?
  → (yes) TTL Delete → TTL Expired Comment → TTL Cleanup → Status: Inactive (TTL)
```

## Install parameters

| Parameter | Where it's used |
|-----------|----------------|
| `repository` | All GitHub nodes (triggers, comments, deployments, reactions) |
| `ssh_secret` | Setup App node SSH authentication |
| `ec2_key_name` | Create Instance node — key pair injected into new instances |

## Memory

The app uses one namespace: `preview-envs`

Each entry stores: `pr_number`, `instance_id`, `instance_ip`, `app_name`, `created_at`

## What's safe to change

- **`scripts/preview-setup.sh`** — edit to match your app's runtime and setup. Receives `PR_NUMBER` and `REPO_URL` as environment variables.
- **Comment text** — any of the `github.createIssueComment` nodes
- **TTL duration** — the `check-ttl` node expression
- **EC2 settings** — instance type, AMI, subnet, security group on the Create Instance node

## What not to change (without understanding the flow)

- **Memory namespace and match keys** — all flows read/write `preview-envs` keyed on `pr_number`
- **Edge wiring between core nodes** — the sequence matters
- **Integration references** — wired at install time

## Common issues

**SSH authentication fails:**
The secret doesn't exist, has the wrong key name, or the private key doesn't match the EC2 key pair. Check that the secret key name is `private-key` and the EC2 key pair name matches what was set during install.

**Setup script fails:**
The default `scripts/preview-setup.sh` sets up a Node.js app. Edit it in the **Files** tab to match your stack. Check the SSH node's `stderr` for the actual error.

**Health check returns non-200:**
The setup script checks `localhost:3000/`. Update both the script and the `http-health-check` node if your app uses a different port or path.
