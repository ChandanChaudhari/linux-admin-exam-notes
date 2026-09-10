# RHCSA — Systemd & Service Management

> RHCSA EX200 revision notes: systemd, services, targets, unit files, dependencies, masking, logs, and troubleshooting.

---

## 1. systemd

`systemd` is the system and service manager used by modern RHEL/AlmaLinux systems. It normally runs as **PID 1**.

The main management command is:

```bash
systemctl
```

### Mental model

```text
systemd
├── services
├── targets
├── sockets
├── mounts
└── timers
```

---

## 2. systemd Units

A **unit** is a resource managed by systemd.

Common unit types:

| Unit | Purpose |
|---|---|
| `.service` | System service |
| `.target` | Group of units / system state |
| `.socket` | Socket activation |
| `.mount` | Filesystem mount |
| `.timer` | Scheduled task |
| `.path` | Path-based activation |

Examples:

```text
sshd.service
multi-user.target
graphical.target
```

You can usually omit `.service`:

```bash
systemctl status sshd
```

is equivalent to:

```bash
systemctl status sshd.service
```

---

# 3. Basic Service Management

Assume the service is `httpd`.

### Start

Starts the service immediately:

```bash
systemctl start httpd
```

It does not automatically enable the service for future boots.

### Stop

Stops the service immediately:

```bash
systemctl stop httpd
```

It does not change boot enablement.

### Restart

Stops and starts the service again:

```bash
systemctl restart httpd
```

### Reload

Reloads configuration without a full restart, if supported by the service:

```bash
systemctl reload httpd
```

---

# 4. Enable and Disable

### Enable

Configures the service to start automatically during boot:

```bash
systemctl enable httpd
```

`enable` does not necessarily start it immediately.

### Disable

Prevents automatic startup at boot:

```bash
systemctl disable httpd
```

`disable` does not necessarily stop a currently running service.

---

# 5. Enable + Start Now

Very important RHCSA command:

```bash
systemctl enable --now httpd
```

Meaning:

```text
enable → start automatically at boot
--now  → start immediately
```

Opposite operation:

```bash
systemctl disable --now httpd
```

Meaning:

```text
disable → don't start automatically at boot
--now   → stop immediately
```

### Exam shortcut

If the question says:

> Start the service now and make it start after reboot.

Use:

```bash
systemctl enable --now SERVICE
```

---

# 6. Check Service Status

Full status:

```bash
systemctl status httpd
```

Shows information such as:

- Loaded state
- Active state
- Main PID
- Recent journal messages
- Process information

Example:

```text
Loaded: loaded
Active: active (running)
```

### Loaded vs Active

```text
Loaded → systemd knows about the unit
Active → service is currently running/active
```

---

# 7. `is-active`

Checks the current runtime state:

```bash
systemctl is-active httpd
```

Typical output:

```text
active
```

Other states can include:

```text
inactive
failed
```

Memory:

```text
is-active → Is it running NOW?
```

---

# 8. `is-enabled`

Checks boot enablement:

```bash
systemctl is-enabled httpd
```

Typical output:

```text
enabled
```

or:

```text
disabled
```

Memory:

```text
is-enabled → Is it configured for BOOT?
```

### Critical distinction

```text
ACTIVE  = NOW
ENABLED = BOOT
```

---

# 9. Service vs Process

A **service** is managed by systemd.

A **process** is a running instance of a program.

Manage services with:

```bash
systemctl
```

Inspect processes with:

```bash
ps
pgrep
top
```

Mental model:

```text
systemd
   ↓
service/unit
   ↓
process(es)
```

---

# 10. Systemd Targets

A **target** is a systemd unit that groups other units together to represent a particular system state.

Important targets:

```text
multi-user.target
graphical.target
rescue.target
emergency.target
```

### `multi-user.target`

Normal non-graphical multi-user state. Typical for servers.

### `graphical.target`

Graphical system state.

### `rescue.target`

Minimal recovery/troubleshooting environment.

### `emergency.target`

Even more minimal recovery environment.

---

# 11. Runlevels → systemd Targets

Traditional mappings:

| Runlevel | systemd target |
|---:|---|
| 0 | `poweroff.target` |
| 1 | `rescue.target` |
| 3 | `multi-user.target` |
| 5 | `graphical.target` |
| 6 | `reboot.target` |

Most important:

```text
3 → multi-user.target
5 → graphical.target
```

---

# 12. Default Boot Target

Show the current default:

```bash
systemctl get-default
```

Change the default for future boots:

```bash
systemctl set-default graphical.target
```

or:

```bash
systemctl set-default multi-user.target
```

### Important

`set-default` affects the **next boot**, not the current system state.

---

# 13. Switch Target Immediately

Immediately switch the running system:

```bash
systemctl isolate graphical.target
```

or:

```bash
systemctl isolate multi-user.target
```

### Critical distinction

```text
set-default → NEXT BOOT
isolate     → CURRENT SYSTEM
```

Memory:

> **SET = future**  
> **ISOLATE = now**

---

# 14. Target and Unit Listing

List currently loaded target units:

```bash
systemctl list-units --type=target
```

List installed unit files and their states:

```bash
systemctl list-unit-files
```

Show a unit's dependency tree:

```bash
systemctl list-dependencies httpd
```

---

# 15. Unit File Structure

Three important sections:

```ini
[Unit]

[Service]

[Install]
```

### `[Unit]`

General information, dependencies, and ordering:

```ini
[Unit]
Description=My Application
After=network.target
Requires=network.target
```

Common directives:

```text
Description=
After=
Before=
Requires=
Wants=
```

### `[Service]`

Defines how the service runs:

```ini
[Service]
ExecStart=/usr/local/bin/myapp
Restart=on-failure
User=myuser
```

Common directives:

```text
ExecStart=   → command used to start the service
ExecStop=    → command used to stop it
Restart=     → restart behavior
User=        → user running the service
Environment= → environment variables
```

### `[Install]`

Defines integration when the service is enabled:

```ini
[Install]
WantedBy=multi-user.target
```

---

# 16. Unit File Locations

### Vendor/package units

```text
/usr/lib/systemd/system/
```

Usually installed by packages.

### Administrator/custom units

```text
/etc/systemd/system/
```

Used for administrator-created units, customizations, and overrides.

Avoid directly editing vendor files in `/usr/lib/systemd/system/` because package updates can overwrite them.

---

# 17. Inspect Unit Files

Display a unit file and relevant drop-ins:

```bash
systemctl cat httpd
```

Create an administrator override:

```bash
systemctl edit httpd
```

After modifying unit configuration:

```bash
systemctl daemon-reload
```

`daemon-reload` makes systemd **re-read unit files**. It does not restart the service.

Typical sequence:

```bash
systemctl edit myservice
systemctl daemon-reload
systemctl restart myservice
```

Mental model:

```text
daemon-reload → reread unit files
restart       → restart service
```

---

# 18. Dependencies and Ordering

View dependencies:

```bash
systemctl list-dependencies httpd
```

### `After=`

Specifies **ordering**:

```ini
After=network.target
```

Meaning:

> If both units are being started, start this unit after `network.target`.

`After=` does **not** itself create a dependency or start the other unit.

```text
After= → ORDER
```

### `Requires=`

Creates a stronger dependency relationship:

```ini
Requires=network.target
```

```text
Requires= → DEPENDENCY
```

### `Wants=`

Creates a weaker dependency relationship:

```ini
Wants=network.target
```

Remember:

```text
After=     → ordering
Requires=  → stronger dependency
Wants=     → weaker dependency
```

Dependency and ordering are not the same thing.

---

# 19. Masking

Mask a service:

```bash
systemctl mask httpd
```

A masked unit is linked to `/dev/null`, preventing normal attempts to start it.

Remove the mask:

```bash
systemctl unmask httpd
```

### Disable vs Mask

```bash
systemctl disable httpd
```

Means:

> Don't automatically start it at boot.

Manual start is still possible:

```bash
systemctl start httpd
```

Whereas:

```bash
systemctl mask httpd
```

means:

> Prevent normal attempts to start the unit.

Memory:

```text
disable → not automatic
mask    → cannot normally start
```

---

# 20. journalctl

`journalctl` queries logs stored by the systemd journal.

### All journal entries

```bash
journalctl
```

### Logs for a service

```bash
journalctl -u httpd
```

Here:

```text
-u → --unit
```

### Logs from current boot

```bash
journalctl -b
```

For one service during current boot:

```bash
journalctl -u httpd -b
```

### Follow logs live

```bash
journalctl -u httpd -f
```

`-f` follows new entries as they appear, similar to:

```bash
tail -f
```

### Logs since a time

```bash
journalctl -u httpd --since "10 minutes ago"
```

---

# 21. Service Troubleshooting Workflow

When a service fails, use a structured workflow.

### Step 1 — Check status

```bash
systemctl status httpd
```

Look for failure state, exit status, and recent messages.

### Step 2 — Check journal

```bash
journalctl -u httpd -b
```

Find the actual error.

### Step 3 — Inspect the unit

```bash
systemctl cat httpd
```

Check `ExecStart`, paths, dependencies, and overrides.

### Step 4 — Check dependencies

```bash
systemctl list-dependencies httpd
```

### Step 5 — Fix the problem

Possible causes:

- Configuration error
- Missing file
- Permission problem
- Dependency failure
- Incorrect path
- Port conflict

### Step 6 — Restart

```bash
systemctl restart httpd
```

### Step 7 — Verify

```bash
systemctl status httpd
```

or:

```bash
systemctl is-active httpd
```

---

# 22. Service Command Cheat Sheet

```bash
systemctl start SERVICE
systemctl stop SERVICE
systemctl restart SERVICE
systemctl reload SERVICE

systemctl enable SERVICE
systemctl disable SERVICE

systemctl enable --now SERVICE
systemctl disable --now SERVICE

systemctl status SERVICE
systemctl is-active SERVICE
systemctl is-enabled SERVICE

systemctl mask SERVICE
systemctl unmask SERVICE

systemctl cat SERVICE
systemctl daemon-reload
systemctl list-dependencies SERVICE
```

---

# 23. Target Command Cheat Sheet

```bash
systemctl get-default

systemctl set-default multi-user.target
systemctl set-default graphical.target

systemctl isolate multi-user.target
systemctl isolate graphical.target

systemctl list-units --type=target
systemctl list-dependencies multi-user.target
```

---

# 24. Journal Command Cheat Sheet

```bash
journalctl
journalctl -u SERVICE
journalctl -b
journalctl -u SERVICE -b
journalctl -u SERVICE -f
journalctl -u SERVICE --since "10 minutes ago"
```

---

# 25. Common RHCSA Scenarios

### Start now + enable at boot

```bash
systemctl enable --now httpd
```

### Stop now

```bash
systemctl stop httpd
```

### Disable boot startup

```bash
systemctl disable httpd
```

### Prevent normal starts

```bash
systemctl mask httpd
```

### Allow normal starts again

```bash
systemctl unmask httpd
```

### Boot into non-graphical multi-user mode

```bash
systemctl set-default multi-user.target
```

### Immediately switch to graphical mode

```bash
systemctl isolate graphical.target
```

### Find current-boot httpd logs

```bash
journalctl -u httpd -b
```

### Inspect unit configuration

```bash
systemctl cat httpd
```

### Re-read changed unit files

```bash
systemctl daemon-reload
```

---

# 26. High-Value RHCSA Mental Models

```text
ACTIVE   = NOW
ENABLED  = BOOT
```

```text
start    = start now
enable   = start at boot
```

```text
stop     = stop now
disable  = don't start automatically
```

```text
disable  = not automatic
mask     = cannot normally start
```

```text
set-default = next boot
isolate     = current system
```

```text
After=     = ordering
Requires=  = dependency
Wants=     = weaker dependency
```

```text
daemon-reload = reread unit files
restart       = restart service
```

---

# 27. RHCSA Exam Time-Saving Hack

When a question mentions both **current state** and **boot behavior**, split the requirements:

```text
NOW  → start/stop
BOOT → enable/disable
```

Example:

> Make SSH run now and start automatically after reboot.

Think:

```text
NOW  → start
BOOT → enable
```

Then combine:

```bash
systemctl enable --now sshd
```

Opposite:

> Stop SSH now and prevent automatic startup.

```bash
systemctl disable --now sshd
```

This mental approach saves time and prevents mixing up `start`/`enable` or `stop`/`disable`.

---

# 28. Final RHCSA Checklist

You should be able to explain and use:

- [x] systemd and PID 1
- [x] systemd units
- [x] `.service` and `.target`
- [x] `start`, `stop`, `restart`, `reload`
- [x] `enable`, `disable`
- [x] `enable --now`
- [x] `disable --now`
- [x] `status`
- [x] `is-active`
- [x] `is-enabled`
- [x] Service vs process
- [x] Targets
- [x] `multi-user.target`
- [x] `graphical.target`
- [x] `rescue.target`
- [x] `emergency.target`
- [x] Runlevel mappings
- [x] `get-default`
- [x] `set-default`
- [x] `isolate`
- [x] Unit file sections
- [x] `/usr/lib/systemd/system/`
- [x] `/etc/systemd/system/`
- [x] `systemctl cat`
- [x] `systemctl edit`
- [x] `daemon-reload`
- [x] `After=`
- [x] `Requires=`
- [x] `Wants=`
- [x] `list-dependencies`
- [x] `mask` / `unmask`
- [x] `journalctl`
- [x] Service troubleshooting

---

## Topic Complete

**RHCSA EX200 — Systemd & Service Management**
