# Process Management

## 1. What Is a Process?

A **process** is a running instance of a program or command.

Every running process has a unique **PID (Process ID)**.

Example:

```bash
sleep 100 &
```

Output may look like:

```text
[1] 2458
```

- `1` = shell job number
- `2458` = PID

> **Important:** Job numbers and PIDs are different things.

---

## 2. Viewing Processes with `ps`

### Basic `ps`

```bash
ps
```

Shows processes associated with the current terminal/session.

### `ps aux`

```bash
ps aux
```

Shows processes from all users, including processes that do not have a controlling terminal.

The options are BSD-style:

| Option | Meaning |
|---|---|
| `a` | Show processes of all users that have a terminal |
| `u` | User-oriented detailed format |
| `x` | Include processes without a controlling terminal |

Use:

```bash
ps aux
```

rather than commonly written `ps -aux`.

### `ps -ef`

```bash
ps -ef
```

Shows all processes in a full-format listing.

Important columns include:

| Column | Meaning |
|---|---|
| `UID` | User who owns the process |
| `PID` | Process ID |
| `PPID` | Parent Process ID |
| `C` | CPU utilization/scheduling information |
| `STIME` | Start time |
| `TTY` | Controlling terminal |
| `TIME` | CPU time used |
| `CMD` | Command that started the process |

---

## 3. PID and PPID

### PID

**PID = Process ID**

It uniquely identifies a running process.

Example:

```text
PID
2500
```

### PPID

**PPID = Parent Process ID**

Processes are normally created by another process. The process that creates another process is its **parent**.

Example:

```text
Parent process
     |
     +---- Child process
```

You can inspect a specific process with:

```bash
ps -p 2500 -o pid,ppid,stat,cmd
```

Explanation:

- `-p 2500` → select PID 2500
- `-o` → choose which columns to display
- `pid` → process ID
- `ppid` → parent process ID
- `stat` → process state
- `cmd` → command

---

## 4. PID 1

On a systemd-based RHEL/AlmaLinux system, **systemd normally runs as PID 1**.

Check it with:

```bash
ps -p 1
```

or:

```bash
pgrep systemd
```

PID 1 is responsible for starting and managing many system services and processes.

---

## 5. Finding Processes

### `pgrep`

`pgrep` searches the process table by process name/pattern.

Example:

```bash
pgrep sshd
```

This returns the PIDs of matching `sshd` processes.

### `ps` with `grep`

```bash
ps -ef | grep sshd
```

Here:

- `ps -ef` → produces the process list
- `|` → sends that output to another command
- `grep sshd` → displays lines containing `sshd`

---

## 6. Monitoring Processes with `top`

```bash
top
```

`top` provides an interactive, continuously updating view of processes and system resource usage.

Useful keys inside `top`:

| Key | Action |
|---|---|
| `P` | Sort by CPU usage |
| `M` | Sort by memory usage |
| `k` | Send a signal to a process |
| `q` | Quit `top` |

---

## 7. Process States

A process can have different states.

| State | Meaning |
|---|---|
| `R` | Running or runnable |
| `S` | Sleeping |
| `D` | Uninterruptible sleep |
| `T` | Stopped |
| `Z` | Zombie |

Check the state with:

```bash
ps -p PID -o pid,ppid,stat,cmd
```

Example:

```bash
ps -p 2500 -o pid,ppid,stat,cmd
```

---

## 8. Zombie Processes

A **zombie** is a process that has finished execution but whose parent has not yet collected its exit status.

It may appear with state:

```text
Z
```

Important:

> You generally do not solve a zombie by killing the zombie itself. The parent process needs to reap the child.

---

# 9. Foreground and Background Processes

## Foreground

A normal command runs in the foreground:

```bash
sleep 100
```

The terminal is occupied until the command finishes.

## Background

Add `&` to run the command in the background:

```bash
sleep 100 &
```

The shell immediately gives you the prompt again.

Example:

```text
[1] 2458
```

- `1` → job number
- `2458` → PID

---

# 10. Job Control

Linux shells provide commands for controlling jobs.

## `jobs`

```bash
jobs
```

Lists jobs started from the current shell.

Example:

```text
[1]+  Running    sleep 100 &
```

The number `1` is the **job number**.

---

## `Ctrl+Z`

Press:

```text
Ctrl+Z
```

This sends **SIGTSTP** to the foreground process and normally suspends it.

It does **not** terminate the process.

Example:

```bash
sleep 1000
```

Press `Ctrl+Z`.

The job becomes stopped.

---

## `bg`

Continue a stopped job in the background:

```bash
bg %1
```

Here:

- `bg` → continue a stopped job in the background
- `%1` → shell job number 1

> `bg` uses a **job number**, not a PID.

---

## `fg`

Bring a background job to the foreground:

```bash
fg %1
```

Here `%1` means shell job number 1.

Without specifying a job:

```bash
fg
```

normally brings the current/most recent job to the foreground.

---

# 11. `Ctrl+C` vs `Ctrl+Z`

These two shortcuts are very important.

| Shortcut | Signal | Typical effect |
|---|---|---|
| `Ctrl+C` | `SIGINT` | Interrupts/terminates the foreground command |
| `Ctrl+Z` | `SIGTSTP` | Suspends/stops the foreground command |

### Easy way to remember

```text
Ctrl+C → Cancel/Interrupt
Ctrl+Z → Pause/Suspend
```

---

# 12. Signals

A **signal** is a software notification sent to a process.

The `kill` command is primarily a **signal-sending command**.

> `kill` does not always mean "kill the process."

---

## `kill PID`

```bash
kill 2500
```

By default, this sends:

```text
SIGTERM (15)
```

It asks the process to terminate gracefully.

---

## Explicit SIGTERM

These are equivalent:

```bash
kill 2500
```

```bash
kill -15 2500
```

```bash
kill -SIGTERM 2500
```

SIGTERM gives the process an opportunity to clean up before exiting.

---

## SIGKILL

```bash
kill -9 2500
```

or:

```bash
kill -SIGKILL 2500
```

This sends **SIGKILL (9)**.

SIGKILL forcefully terminates the process.

A process **cannot catch, block, or handle SIGKILL**.

Use SIGKILL only when a normal termination request does not work.

---

## Stop a Process

```bash
kill -STOP 2500
```

Sends `SIGSTOP` and stops the process.

---

## Continue a Process

```bash
kill -CONT 2500
```

Sends `SIGCONT` and resumes a stopped process.

---

## List Signals

```bash
kill -l
```

Lists available signals.

Important signals for RHCSA:

| Number | Signal | Common purpose |
|---:|---|---|
| `1` | `SIGHUP` | Hangup |
| `2` | `SIGINT` | Interrupt |
| `9` | `SIGKILL` | Force termination |
| `15` | `SIGTERM` | Graceful termination |
| `18` | `SIGCONT` | Continue |
| `19` | `SIGSTOP` | Stop |
| `20` | `SIGTSTP` | Terminal stop/suspend |

---

# 13. `kill` vs `pkill` vs `killall`

## `kill`

Use `kill` when you know the **PID**.

```bash
kill 2500
```

This targets PID 2500.

---

## `pkill`

Use `pkill` when you want to match a **process name or pattern**.

```bash
pkill nginx
```

This can affect multiple matching processes.

Example:

```bash
pkill -9 nginx
```

Sends SIGKILL to matching `nginx` processes.

> Be careful with `pkill` because a name/pattern can match multiple processes.

---

## `killall`

`killall` can also send a signal to processes matching a process name.

Example:

```bash
killall sleep
```

This can affect multiple processes named `sleep`.

---

## Quick Rule

```text
Known PID?
    ↓
kill PID

Known process name/pattern?
    ↓
pkill name
```

---

# 14. Safe Process Termination Workflow

If a process is not responding, do not immediately use `kill -9`.

A better workflow is:

### Step 1 — Identify the process

```bash
ps -p 2500 -o pid,ppid,stat,cmd
```

### Step 2 — Request graceful termination

```bash
kill 2500
```

This sends SIGTERM.

### Step 3 — Check again

```bash
ps -p 2500 -o pid,ppid,stat,cmd
```

### Step 4 — Force termination only if necessary

```bash
kill -9 2500
```

---

# 15. Important RHCSA Rules

### Rule 1 — PID vs Job Number

```text
PID       → 2500
Job ID    → %1
```

Therefore:

```bash
kill 2500
```

but:

```bash
fg %1
bg %1
```

Do not confuse them.

---

### Rule 2 — SIGTERM vs SIGKILL

```bash
kill 2500
```

→ SIGTERM → graceful request

```bash
kill -9 2500
```

→ SIGKILL → forceful termination

Preferred escalation:

```text
SIGTERM → wait/check → SIGKILL if necessary
```

---

### Rule 3 — `Ctrl+Z` Does Not Kill

```text
Ctrl+Z → suspend
Ctrl+C → interrupt
```

A suspended process can be resumed:

```bash
bg %1
```

or:

```bash
fg %1
```

---

# 16. RHCSA Exam Time-Saver

When you need information about **one specific PID**, use:

```bash
ps -p PID -o pid,ppid,stat,cmd
```

Example:

```bash
ps -p 2500 -o pid,ppid,stat,cmd
```

This is cleaner than:

```bash
ps aux | grep 2500
```

because `ps -p` directly selects the process and avoids accidentally matching the `grep` command itself.

---

# 17. Quick Revision

```text
Process       → Running instance of a program
PID           → Process ID
PPID          → Parent Process ID
PID 1         → systemd on normal systemd-based AlmaLinux/RHEL systems

ps aux        → Detailed process list
ps -ef        → Full process list
top           → Interactive process monitoring
pgrep name    → Find PIDs by process name

jobs          → List shell jobs
bg %1         → Continue job 1 in background
fg %1         → Bring job 1 to foreground

Ctrl+C        → SIGINT / interrupt
Ctrl+Z        → SIGTSTP / suspend

kill PID      → SIGTERM by default
kill -15 PID  → SIGTERM
kill -9 PID   → SIGKILL
kill -STOP PID → Stop
kill -CONT PID → Continue

pkill name    → Signal processes by name/pattern
killall name  → Signal processes by name

R → Running/runnable
S → Sleeping
D → Uninterruptible sleep
T → Stopped
Z → Zombie
```

---

## Practice Before Moving On

1. You have PID `2500` and it is stopped. How do you continue it?

2. How do you gracefully terminate PID `2500`?

3. PID `2500` refuses SIGTERM. What command would you use to forcefully terminate it?

4. Give one clean command that displays the PID, PPID, state, and command for PID `2500`.

5. You started:

```bash
sleep 1000
```

Then pressed `Ctrl+Z`.

Give the commands to:
- continue it in the background
- bring it back to the foreground
