# Avado Warm Standby Plan

Last updated: 2026-04-01

## Goal

Keep `avado` as the primary PPV automation host and create a warm standby environment on the offsite Synology home cloud.

Target outcome:

- `avado` remains the active workhorse
- Synology hosts a Linux VM that can take over after `avado` failure
- offsite backups continue to flow from `avado` to Synology
- Hermes and active work repos can be restored and resumed from the standby VM with minimal rebuild work

This is not a hot standby or ASR-style continuous failover design.

It is a warm standby design:

- backup-protected
- pre-provisioned compute target
- documented promotion workflow
- lower complexity than full replication

## Current State

Primary host:

- hostname: `ppv-automation-server`
- user: `eao168`
- LAN IP at time of setup: `192.168.86.26`
- Tailscale IP at time of setup: `100.67.195.109`
- current workload:
  - `lead-crawler`
  - `ads-ops-job`
  - future Hermes installation and repo-hosted remote development

Offsite host:

- Synology hostname: `AoFamilyCloud`
- model family from kernel string: `synology_v1000_rs822+`
- DSM: `7.2.1-69057 Update 11`
- RAM detected: about `32 GB`
- `/volume1` free space: about `24 TB`
- Tailscale IP at time of setup: `100.71.176.68`

Offsite backup already implemented:

- `restic` repository id: `8272798f`
- repository target: `sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic`
- scheduled root backup on `avado`: daily at `3:20 AM`

## Architecture

### Primary

`avado` remains the live execution environment for:

- crawler processing
- ads ops
- future Hermes operation
- active git repos used through VS Code Remote SSH

### Standby

Create one Ubuntu VM on the Synology as the warm standby target.

Suggested standby VM role:

- host name: `ppv-standby`
- purpose: receive restored `avado` environment and be ready for manual promotion if `avado` fails

### Backup And Restore Flow

1. `avado` writes encrypted `restic` backups to Synology over Tailscale/SFTP
2. Synology stores both:
   - backup repository
   - standby VM disks
3. In a failure event:
   - boot the standby VM
   - restore the latest `avado` snapshot
   - validate services
   - promote standby to active

## Why This Design

This design gives a better recovery path than backup-only because:

- the target compute environment already exists
- Tailscale and remote access patterns are already known
- Hermes can be restored into a prepared Linux host rather than a blank machine
- the recovery process becomes a restore-and-promote workflow instead of a full environment redesign under pressure

## Synology Readiness Summary

Based on current inspection:

- enough RAM exists for a standby VM
- enough storage exists for a standby VM
- Tailscale is installed and working
- SFTP is enabled and working
- Virtual Machine Manager does not appear to be installed yet

That means the first Synology-side prerequisite is:

- install `Virtual Machine Manager` in DSM if it is supported on this unit

## Standby VM Specification

Start conservative. The standby VM does not need to match `avado` perfectly on day one.

Recommended initial VM spec:

- OS: Ubuntu 24.04 LTS
- vCPU: `4`
- RAM: `8 GB` minimum, `12 to 16 GB` preferred if the Synology can spare it
- Disk: `120 GB` system disk to start
- Network: bridged or normal Synology VM network with outbound internet access

Scale up later if failover testing shows the crawler or Hermes need more headroom.

## Phase Plan

### Phase 1. Synology VM Bootstrap

In DSM:

1. Install `Virtual Machine Manager`
2. Create a new Ubuntu VM
3. Install Ubuntu 24.04
4. Enable SSH
5. Install Tailscale
6. Join the same tailnet
7. Create primary user `eao168`

Validation after phase 1:

- VM reachable by SSH
- VM reachable over Tailscale
- outbound internet works
- disk and memory are stable

### Phase 2. Standby Base Tooling

Install base packages on the VM:

```bash
sudo apt update
sudo apt install -y \
  curl \
  git \
  openssh-server \
  rsync \
  bzip2 \
  python3 \
  python3-pip \
  python3-venv \
  cron
```

Install Tailscale:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo systemctl enable --now tailscaled
sudo tailscale up
```

Install `restic` under `/home/eao168/.local/bin/restic`.

Validation after phase 2:

- `ssh` works
- `tailscale ip -4` works
- `restic version` works

### Phase 3. Recovery Bootstrap Material

Store these on the standby VM before any disaster:

- `restic` passphrase
- Synology backup SSH key pair used by `avado`
- copy of the recovery runbook
- copy of this standby plan

Without these bootstrap items, recovery is still possible but slower and more manual.

### Phase 4. First Full Restore Drill Into The VM

Restore the latest `avado` snapshot into a staging directory:

```bash
sudo mkdir -p /restore
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  restore latest --target /restore
```

Then lay the files into place:

```bash
sudo rsync -aHAX /restore/home/eao168/ /home/eao168/
sudo rsync -aHAX /restore/etc/ /etc/
sudo rsync -aHAX /restore/usr/local/ /usr/local/
sudo rsync -aHAX /restore/opt/ /opt/
sudo rsync -aHAX /restore/var/spool/cron/crontabs/ /var/spool/cron/crontabs/
sudo rsync -aHAX /restore/var/lib/tailscale/ /var/lib/tailscale/
sudo chown -R eao168:eao168 /home/eao168
```

Validation after phase 4:

- repo directories appear under `/home/eao168`
- app configs exist
- cron files exist
- Tailscale state can be validated

### Phase 5. App Validation On Standby VM

Validate workload readiness for:

- `lead-crawler`
- `ads-ops-job`
- SSH and VS Code Remote SSH access
- future Hermes install target

Minimum checks:

- Python virtualenv creation works
- app dependencies can install
- logs are writable
- outbound network access works
- backup scripts are present

### Phase 6. Hermes On Primary, Restore To Standby

Hermes should still be installed first on `avado`, because `avado` is the primary workhorse.

Once Hermes is installed on `avado` and active work repos are moved there:

- confirm Hermes files and config live in the backed-up scope
- verify the standby VM can restore that state
- test VS Code Remote SSH against the standby VM

The standby VM is not required to run Hermes full-time.
It only needs to be able to assume that role when promoted.

## Promotion Procedure

If `avado` fails:

1. Confirm `avado` is unavailable or should not be reused
2. Start or connect to the standby VM
3. Verify Tailscale connectivity
4. Pull latest backup metadata
5. Restore latest snapshot if the VM is not already current
6. Validate app directories, configs, and cron
7. Start the workloads needed first
8. Point operators and VS Code to the standby VM instead of `avado`
9. Run a fresh backup from the standby VM after promotion

## Warm Standby Cadence

Recommended cadence:

- daily automated offsite backup from `avado`
- weekly manual snapshot review
- monthly standby restore drill
- after any major environment change:
  - verify the changed path is inside the backup scope
  - verify the standby VM can still restore it

## Database Handling

Current note:

- DB data is already stored to Azure Blob after crawl workflows

If you want the standby VM to become more self-contained, add database dumps into the backup scope as a separate step.

Recommended upgrade:

- daily logical PostgreSQL dump into `/home/eao168/db-backups`
- allow restic to carry those dumps to Synology

That gives the standby VM a more complete application recovery path.

## What “Warm” Means Here

This plan gives:

- prepared compute target
- prepared network path
- backup data already on the same offsite device
- manual but fast recovery path

This plan does not give:

- continuous live replication
- auto-failover
- zero-downtime cutover

## Acceptance Criteria

This warm standby is considered complete when all of the following are true:

- Synology VM exists and boots reliably
- VM is on Tailscale
- VM is reachable by SSH
- `restic` can list the `avado` backup repository from the VM
- the VM has completed at least one full restore drill from the current repository
- `lead-crawler` and `ads-ops-job` directories are present after restore
- Hermes install target path is known and included in the backed-up scope
- restore and promotion steps are documented and repeatable

## Immediate Next Actions

1. Install `Virtual Machine Manager` on the Synology if supported
2. Create the Ubuntu standby VM
3. Bootstrap Tailscale and SSH on the VM
4. Copy recovery bootstrap secrets to the VM securely
5. Run the first restore drill
6. Validate PPV workloads on the standby VM
7. Install Hermes on `avado`
8. Move active work repos intended for Hermes onto `avado`
9. Re-run restore drill after Hermes and repo migration

## Decision Record

Reason for choosing warm standby instead of hot standby:

- lower complexity
- fits Synology/home-cloud environment better
- aligns with current PPV need to keep `avado` as the workhorse
- avoids false confidence from an incomplete pseudo-replication design
- preserves a future path toward something hotter later if needed
