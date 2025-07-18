# Incus-Tools
-----

* [Description](#description)
* [incus-auto-snapshot](#incus-auto-snapshot)
* [incus-repl-instance](#incus-repl-instance)

# Description

This repo contains some usefull script for my [`incus`](https://github.com/lxc/incus/) deployments. Incus is awesome btw!!!

## Incus-auto-snapshot

Since the automatic snapshot engine in incus is not sufficient for me, I have created a small script + some systemd unit files to configure auto-snapshotting with different retention policies.

```
# Install snapshot-engine
curl https://raw.githubusercontent.com/ChrisStro/incus-tools/refs/heads/main/auto-snapshot/install-incus-snapshot.sh | bash -
```

Every instance(or custom storage volume) with the custom user config user.auto-snapshot=true will be targeted by the snapshot-engine, you can apply these settings via instance or profiles

```
# Profile
incus profile set default user.auto-snapshot=true

# Instance
incus config set MYINSTANCE user.auto-snapshot=true

# Exclude instance if applied via profile
incus config set MYINSTANCE user.auto-snapshot=false # other value than 'true'

# Check which instances are targeted
incus-auto-snapshot --list-enabled
```



## Incus-repl-instance

Script and systemd service + timer to trigger pull copy from remote instances.

```
# Install repl-engine
curl https://raw.githubusercontent.com/ChrisStro/incus-tools/refs/heads/main/repl-instance/install-incus-repl-instance.sh | bash -
```

Instances with the custom user configuration user.repl-instance=true will be copied or refreshed to the target project. By default, only the root disk will be copied. All replications run in stateless mode.

Custom volumes with the user configuration user.repl-volume=true will be copied or refreshed to the target storage pool.

You can clear snapshots before starting replication by using the --snap-name-to-clear parameter. This is useful if you have snapshots with a short retention period (frequents) but your replication only runs once per day.

```
# Profile
incus profile set default user.auto-snapshot=true

# Instance
incus config set MYINSTANCE user.auto-snapshot=true

# Exclude instance if applied via profile
incus config set MYINSTANCE user.auto-snapshot=false # other value than 'true'

# Check which source instances and volumes are configured for replication on source
incus-repl-instance --source-server "REMOTE-SERVER" --list-sources

# Use --keep <SNAPSHOT STRING> and --keep-count X to create clones of snapshots to protect against deletion on source. Last snapshot after each run will be cloned
incus-repl-instance --source-server "REMOTE-SERVER" ... --keep "hourly" --keep-count 5
```