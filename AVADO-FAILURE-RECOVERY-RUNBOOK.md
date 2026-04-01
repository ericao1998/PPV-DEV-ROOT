# Avado Failure Recovery Runbook

Last updated: 2026-04-01

## Purpose

This document defines the recovery procedure for the `avado` automation host (`ppv-automation-server`) in the event of hardware failure, OS corruption, or unrecoverable local disk loss.

This recovery plan exists to support the move of primary PPV automation work onto `avado`, including:

- `lead-crawler`
- `ads-ops-job`
- future Hermes install and operation
- remote-first development from VS Code over SSH/Tailscale

This is a rebuild-and-restore design, not a bare-metal image design.

Related document:

- `AVADO-WARM-STANDBY-PLAN.md` defines the standby-VM target architecture on the Synology home cloud and the intended promotion workflow after `avado` failure.

## Current Recovery Architecture

Primary source host:

- host: `ppv-automation-server`
- user: `eao168`
- current Tailscale IPv4 at time of setup: `100.67.195.109`

Offsite backup target:

- host: `AoFamilyCloud` (Synology)
- current Tailscale IPv4 at time of setup: `100.71.176.68`
- backup transport: `SFTP` over `Tailscale`

Backup engine:

- tool: `restic`
- repository id: `8272798f`
- repository location: `sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic`

Automation installed on `avado`:

- root backup script: `/usr/local/sbin/avado-restic-backup`
- schedule file: `/etc/cron.d/avado-restic-backup`
- schedule: daily at `3:20 AM`
- log path: `/var/log/avado-backup/restic.log`

## Protected Paths

The root-level automated backup covers:

- `/home/eao168`
- `/etc`
- `/opt`
- `/usr/local`
- `/var/lib/tailscale`
- `/var/spool/cron/crontabs`

Exclusions include:

- `.git`
- `node_modules`
- `venv`
- `.venv`
- Python caches and common local caches

## What This Backup Does Not Guarantee

This backup does not provide:

- bare-metal restore
- bootloader recreation
- partition table recreation
- automatic package reinstallation from apt without additional package manifests
- point-in-time database recovery unless the database is separately dumped or replicated elsewhere

Current operational note:

- lead-crawler database data is already pushed to Azure Blob after crawl workflows
- if full host-level database recovery is required independently of that pipeline, add explicit PostgreSQL dumps into a backed-up path

## Critical Recovery Dependencies

Do not leave these known-good recovery items only on `avado`.

At least one separate secure copy should exist for:

- `restic` password file: `/home/eao168/.config/restic/passphrase`
- Synology backup SSH private key: `/home/eao168/.ssh/id_synology_backup_ed25519`
- Synology backup SSH public key: `/home/eao168/.ssh/id_synology_backup_ed25519.pub`
- this runbook

Without the `restic` passphrase and backup SSH key, full-box recovery becomes slower and more manual.

## Daily Verification

To check that scheduled backups are continuing:

```bash
cat /etc/cron.d/avado-restic-backup
tail -n 100 /var/log/avado-backup/restic.log
```

To inspect repository snapshots:

```bash
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  snapshots
```

## Manual Backup Trigger

To force a full root-level offsite backup on demand:

```bash
sudo /usr/local/sbin/avado-restic-backup
```

## Failure Scenarios Covered

This runbook covers:

- total loss of the `avado` box
- replacement of `avado` with a new Ubuntu machine
- migration of the backed-up automation environment to a new Linux host
- restoration of the working environment needed to run Hermes and the PPV repos from `avado`

## Replacement Box Requirements

Provision a new Ubuntu host with:

- Ubuntu 24.04 LTS or compatible modern Ubuntu release
- SSH access
- outbound internet access
- Tailscale support
- enough storage for the restored working set

Suggested minimum:

- 2 to 4 vCPU
- 8 to 16 GB RAM
- 100+ GB disk for current PPV automation footprint

## Full Restore Procedure

### 1. Provision The New Host

Install Ubuntu and ensure SSH access is working.

Install base packages:

```bash
sudo apt update
sudo apt install -y curl openssh-server bzip2 rsync
```

### 2. Install Tailscale

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo systemctl enable --now tailscaled
sudo tailscale up
```

Approve the device in the Tailscale admin flow.

Confirm Tailscale connectivity:

```bash
tailscale ip -4
ping -c 3 100.71.176.68
```

### 3. Recreate The Primary User

If `eao168` does not already exist:

```bash
sudo useradd -m -s /bin/bash eao168
sudo passwd eao168
```

Create required directories:

```bash
sudo -u eao168 mkdir -p /home/eao168/.ssh
sudo -u eao168 mkdir -p /home/eao168/.config/restic
sudo -u eao168 mkdir -p /home/eao168/.local/bin
```

### 4. Restore Bootstrap Secrets

Before the full restore can run cleanly, place the following back onto the new host:

- `/home/eao168/.config/restic/passphrase`
- `/home/eao168/.ssh/id_synology_backup_ed25519`
- `/home/eao168/.ssh/id_synology_backup_ed25519.pub`

Set permissions:

```bash
sudo chown -R eao168:eao168 /home/eao168/.ssh /home/eao168/.config /home/eao168/.local
sudo chmod 700 /home/eao168/.ssh
sudo chmod 600 /home/eao168/.ssh/id_synology_backup_ed25519
sudo chmod 644 /home/eao168/.ssh/id_synology_backup_ed25519.pub
sudo chmod 600 /home/eao168/.config/restic/passphrase
```

### 5. Install Restic

```bash
cd /tmp
curl -L https://github.com/restic/restic/releases/download/v0.18.1/restic_0.18.1_linux_amd64.bz2 -o restic.bz2
bzip2 -df restic.bz2
chmod +x restic
sudo mv restic /home/eao168/.local/bin/restic
sudo chown eao168:eao168 /home/eao168/.local/bin/restic
```

### 6. Confirm Repository Access

```bash
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  snapshots
```

If snapshots list successfully, the repository is reachable.

### 7. Restore Into A Staging Directory

Do not restore directly over `/` first.

```bash
sudo mkdir -p /restore
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  restore latest --target /restore
```

### 8. Restore Files Into Live Paths

Use `rsync` to lay restored files back into place:

```bash
sudo rsync -aHAX /restore/home/eao168/ /home/eao168/
sudo rsync -aHAX /restore/etc/ /etc/
sudo rsync -aHAX /restore/usr/local/ /usr/local/
sudo rsync -aHAX /restore/opt/ /opt/
sudo rsync -aHAX /restore/var/spool/cron/crontabs/ /var/spool/cron/crontabs/
sudo rsync -aHAX /restore/var/lib/tailscale/ /var/lib/tailscale/
```

Repair ownership:

```bash
sudo chown -R eao168:eao168 /home/eao168
```

### 9. Reinstall Packages

Because this is a file-level backup, apt-installed package state may still need to be rebuilt.

At minimum, install the known platform dependencies used by the current host:

```bash
sudo apt update
sudo apt install -y \
  curl \
  git \
  openssh-server \
  rsync \
  bzip2 \
  python3 \
  python3-venv \
  python3-pip \
  cron
```

If PostgreSQL is required on the restored host:

```bash
sudo apt install -y postgresql
```

If future Hermes usage is part of the rebuild target, also install Hermes prerequisites as needed after the host is stable.

### 10. Re-enable Services And Cron

Check cron schedule:

```bash
cat /etc/cron.d/avado-restic-backup
```

If needed, restart services:

```bash
sudo systemctl restart cron
sudo systemctl restart tailscaled
```

Verify existing app services and timers as applicable for:

- crawler jobs
- ads ops jobs
- PostgreSQL
- any future Hermes service or tmux session plan

### 11. Validate The Restored Host

Minimum validation:

```bash
hostname
tailscale ip -4
crontab -l
ls -la /home/eao168
```

Recommended operational checks:

- confirm `lead-crawler` files exist and can start
- confirm `ads-ops-job` files exist and can start
- confirm outbound internet access
- confirm Synology is reachable over Tailscale
- run a fresh manual backup after restore:

```bash
sudo /usr/local/sbin/avado-restic-backup
```

## Hermes Migration Note

The reason this recovery path matters is that PPV intends to shift more primary work onto `avado`, including Hermes and repo-local remote development.

Implications:

- Hermes should be installed and operated on `avado`, not as a critical dependency only on a local Windows machine
- active repos that Hermes will work on should live on `avado`
- VS Code should connect to `avado` over SSH or Tailscale so editor, terminal, git, and Hermes all operate on the same filesystem

This backup design protects that future-state workflow, but only if the recovery bootstrap items are also preserved off-box.

## Recommended Hardening Follow-Ups

These are not required for current operation, but they improve recoverability:

- keep a second copy of the `restic` passphrase outside both `avado` and Synology
- keep a second copy of the Synology backup SSH key outside `avado`
- export package manifests regularly:

```bash
dpkg --get-selections > /home/eao168/backup-scripts/dpkg-selections.txt
apt-mark showmanual > /home/eao168/backup-scripts/apt-manual.txt
```

- include those package manifest files in the normal backup scope
- add periodic restore drills to a disposable Linux VM
- consider a dedicated Synology backup user instead of `EricAdmin`

## Quick Reference

List snapshots:

```bash
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  snapshots
```

Run backup now:

```bash
sudo /usr/local/sbin/avado-restic-backup
```

Restore latest snapshot to staging:

```bash
sudo mkdir -p /restore
export RESTIC_PASSWORD_FILE=/home/eao168/.config/restic/passphrase
/home/eao168/.local/bin/restic \
  -o sftp.command='ssh -i /home/eao168/.ssh/id_synology_backup_ed25519 -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new EricAdmin@100.71.176.68 -s sftp' \
  -r 'sftp:EricAdmin@100.71.176.68:homes/EricAdmin/backups/avado-restic' \
  restore latest --target /restore
```
