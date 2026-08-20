# RHCSA — Permissions & Ownership

## 🎯 Topic Goals

By the end of this topic, you should be able to confidently:

- [ ] Understand Linux ownership
- [ ] Understand `r`, `w`, `x`
- [ ] Understand permissions for files vs directories
- [ ] Use `chmod`
- [ ] Use symbolic and numeric permissions
- [ ] Use `chown`
- [ ] Manage users and groups
- [ ] Understand supplementary groups
- [ ] Understand setuid
- [ ] Understand setgid
- [ ] Understand sticky bit
- [ ] Understand `s`, `S`, `t`, `T`
- [ ] Create shared directories
- [ ] Verify permissions and ownership
- [ ] Solve RHCSA-style permission tasks without memorizing blindly

---

## 1. Linux File Ownership

Every Linux file and directory has:

1. An owner
2. A group
3. Permissions

**Example:**

```bash
-rwxr-xr-- 1 chandan developers file.txt
```

| Field       | Value       |
|-------------|-------------|
| Owner       | `chandan`   |
| Group       | `developers`|
| Permissions | `rwxr-xr--` |

---

## 2. Permission Classes

Linux permissions are divided into three classes:

| Symbol | Meaning      |
|--------|--------------|
| `u`    | User / Owner |
| `g`    | Group        |
| `o`    | Others       |
| `a`    | All          |

**Examples:**

```bash
chmod u+x file   # Add execute permission for the owner
chmod g+r file   # Add read permission for the group
chmod o-r file   # Remove read permission from others
```

---

## 3. Basic Permissions

| Permission | Symbol | Numeric |
|------------|--------|---------|
| Read       | `r`    | 4       |
| Write      | `w`    | 2       |
| Execute    | `x`    | 1       |

**Numeric calculation:**

```text
rwx = 4 + 2 + 1 = 7
rw- = 4 + 2     = 6
r-x = 4 + 1     = 5
r--             = 4
-w-             = 2
--x             = 1
---             = 0
```

---

## 4. File Permissions vs Directory Permissions

> This is extremely important for RHCSA.

### Files

| Perm | Meaning |
|------|---------|
| `r` | Allows reading the file contents |
| `w` | Allows modifying the file contents |
| `x` | Allows executing the file as a program/script (subject to other requirements) |

### Directories

The meaning is different:

| Perm | Meaning |
|------|---------|
| `r` | Allows listing directory entries (e.g. `ls directory`) |
| `w` | Allows modifying directory entries — creating/deleting/renaming (subject to required execute permission) |
| `x` | Allows entering/traversing the directory and accessing entries when permitted |

---

## 5. Understanding `ls -l`

```bash
ls -l file.txt
```

```text
-rwxr-xr-- 1 chandan developers 123 Aug 20 17:00 file.txt
```

**Breakdown:**

```text
- rwx r-x r--
│ │   │   │
│ │   │   └── Others
│ │   └────── Group
│ └────────── Owner
└──────────── File type
```

**First character (file type):**

| Character | Meaning |
|-----------|---------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |

---

## 6. Creating Users

**Create a user:**

```bash
useradd devuser
```

**Change login shell:**

```bash
usermod -s /bin/bash devuser
```

| Part | Meaning |
|------|---------|
| `usermod` | modify existing user |
| `-s` | specify login shell |
| `/bin/bash` | shell |
| `devuser` | user |

**Set password:**

```bash
passwd devuser
```

> ⚠️ Password is entered interactively. Never put real passwords in Git repositories or scripts.

---

## 7. Creating Groups

```bash
groupadd developers
```

Verify:

```bash
grep developers /etc/group
```

---

## 8. Adding Users to Groups

To add a user to a supplementary group:

```bash
usermod -aG developers devuser
```

| Part | Meaning |
|------|---------|
| `usermod` | modify user |
| `-a` | append |
| `-G` | supplementary groups |
| `developers` | group |
| `devuser` | user |

### ⭐ VERY IMPORTANT RHCSA POINT

Use:

```bash
usermod -aG developers devuser
```

**instead of:**

```bash
usermod -G developers devuser
```

**Why?** `-aG` means *"add the group while preserving existing supplementary groups."* Without `-a`, you may **replace** the user's existing supplementary group memberships.

---

## 9. Check User Information

**`/etc/passwd`:**

```bash
grep devuser /etc/passwd
```

Format: `username:x:UID:GID:GECOS:home:shell`

**`/etc/group`:**

```bash
grep developers /etc/group
```

**Best command to check groups:**

```bash
id devuser
```

```text
uid=1001(devuser) gid=1001(devuser) groups=1001(devuser),1002(developers)
```

---

## 10. Creating Directories

```bash
mkdir /opt/project
```

Create parent directories automatically:

```bash
mkdir -p /opt/project/data/logs
```

`-p` = create missing parent directories and don't fail if the target already exists.

---

## 11. Creating Files

```bash
touch /opt/project/config.txt
```

---

## 12. Absolute and Relative Paths

- **Absolute path:** `/opt/project/config.txt` — starts from `/`
- **Relative path:** `config.txt` — depends on the current directory

> For RHCSA tasks, always pay attention to the exact path.

---

## 13. Changing Ownership

```bash
chown root:developers /opt/project/config.txt   # Owner + group
chown root file.txt                              # Owner only
chown :developers file.txt                       # Group only
chown -R root:developers /opt/project            # Recursive
```

`-R` = recursively change ownership of the directory and everything below it.

> ⚠️ Be careful with `-R`.

---

## 14. Viewing Ownership and Permissions

```bash
ls -l /opt/project/config.txt   # For a file
ls -ld /opt/project              # For the directory itself
```

**Important difference:**

| Command | Shows |
|---------|-------|
| `ls -l /shared` | Contents of `/shared` |
| `ls -ld /shared` | `/shared` itself |

---

## 15. Numeric `chmod`

```bash
chmod NUMBER FILE
```

**Example:**

```bash
chmod 640 config.txt
```

| Digit | Target | Value |
|-------|--------|-------|
| 6 | owner | `rw-` |
| 4 | group | `r--` |
| 0 | others | `---` |

Result: `-rw-r-----`

---

## 16. Common Numeric Permissions

| Number | Permissions |
|--------|-------------|
| 777 | `rwxrwxrwx` |
| 755 | `rwxr-xr-x` |
| 750 | `rwxr-x---` |
| 700 | `rwx------` |
| 666 | `rw-rw-rw-` |
| 644 | `rw-r--r--` |
| 640 | `rw-r-----` |
| 600 | `rw-------` |

---

## 17. Symbolic `chmod`

```bash
chmod [who][operator][permission] file
```

```bash
chmod u+x file
chmod g+r file
chmod o-r file
```

---

## 18. `chmod` Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Add permission | `chmod u+x file` → adds execute to owner |
| `-` | Remove permission | `chmod g-w file` → removes write from group |
| `=` | Set exact permission | `chmod u=rw,g=r,o= file` → sets exactly |

> `+` = add · `-` = remove · `=` = set exactly

---

## 19. Symbolic Permission Examples

**Owner rwx, group rx, others none:**

```bash
chmod u=rwx,g=rx,o= file
```

Result: `-rwxr-x---` (equivalent to `chmod 750 file`)

**Owner rw, group r, others none:**

```bash
chmod u=rw,g=r,o= file
```

Result: `-rw-r-----` (equivalent to `chmod 640 file`)

---

## 20. Special Permissions

Linux has three important special permissions:

1. **setuid**
2. **setgid**
3. **sticky bit**

**Mental model:**

| Bit | Effect |
|-----|--------|
| setuid | → effective UID |
| setgid on executable | → effective GID |
| setgid on directory | → group inheritance |
| sticky bit | → protect files in shared directory |

---

## 21. setuid

**What is setuid?**

setuid on an executable causes the process to use the executable **owner's UID** as its effective UID.

```text
-rwsr-xr-x 1 root root program
```

If user `alice` executes it:

```text
Real UID      = alice
Effective UID = root
```

> Alice does **NOT** become root for her entire login session — only the process uses the elevated effective UID.

---

## 22–25. Configuring & Recognizing setuid

**Enable:**

```bash
chmod u+s /usr/local/bin/test
```

**Remove** (only the setuid bit, not normal permissions):

```bash
chmod u-s /usr/local/bin/test
```

**Recognizing setuid:**

| Normal | setuid |
|--------|--------|
| `-rwxr-xr-x` | `-rwsr-xr-x` |

The `s` appears in the **owner's execute position**.

### Lowercase `s` vs Uppercase `S`

| Symbol | Meaning |
|--------|---------|
| `-rwsr-xr-x` | setuid enabled + owner execute enabled |
| `-rwSr-xr-x` | setuid enabled + owner execute **NOT** enabled |

Fix: `chmod u+x file` → `-rwsr-xr-x`

> **Memory rule:** `s` = special bit + execute · `S` = special bit + no execute

---

## 26–29. setgid on Executables

setgid on an executable causes the process to use the **executable file's group** as its effective GID.

```text
-rwxr-sr-x 1 root developers /usr/local/bin/teamtool
```

If Alice executes it:

```text
Real GID      = Alice's normal group
Effective GID = developers
```

**Enable / remove:**

```bash
chmod g+s /usr/local/bin/teamtool
chmod g-s /usr/local/bin/teamtool
```

**Recognizing setgid:**

| Normal | setgid |
|--------|--------|
| `-rwxr-xr-x` | `-rwxr-sr-x` |

The `s` appears in the **group execute position**.

---

## 30–31. setgid on Directories ⭐

> This is one of the most important RHCSA concepts.

If a directory has:
- Group = `developers`
- setgid = enabled

...new files/directories created inside **inherit the directory's group**.

**Example:**

```text
/shared/projects  →  root:developers, setgid enabled
```

```bash
chmod g+s /shared/projects
```

Permissions become: `drwxrws---`

Alice creates `/shared/projects/report.txt` → resulting ownership: `alice:developers`

> **Key concept:** Owner = creator, Group = inherited from setgid directory.

---

## 32. setgid `s` vs `S`

| Symbol | Meaning |
|--------|---------|
| `-rwxr-sr-x` | setgid enabled + group execute enabled |
| `-rwxr-Sr-x` | setgid enabled + group execute **NOT** enabled |

Fix: `chmod g+x file`

---

## 33–38. Sticky Bit

The sticky bit is mainly used on shared directories.

**Purpose:** Users can generally create/use files in the shared directory but **cannot delete or rename files belonging to other users**.

**Classic example — `/tmp`:**

```text
drwxrwxrwt
```

**Enable / remove:**

```bash
chmod +t /shared
chmod -t /shared
```

**Recognizing sticky bit:**

```text
drwxrwxrwt
        ^
        t
```

The `t` appears in the **others execute position**.

**Example:** Alice creates `/shared/alice.txt`; Bob normally cannot delete it just because he has write permission on `/shared` — the sticky bit protects directory entries from ordinary cross-user deletion/renaming.

### `t` vs `T`

| Symbol | Meaning |
|--------|---------|
| `drwxrwxrwt` | sticky bit enabled + others execute enabled |
| `drwxrwxrwT` | sticky bit enabled + others execute **NOT** enabled |

Fix: `chmod o+x /shared` → `drwxrwxrwt`

---

## 39–40. Combined setgid + Sticky Bit ⭐

A common RHCSA shared-directory requirement — `/shared/projects`:

**Requirements:**
- Owner = `root`, Group = `developers`
- Owner/Group/Others = `rwx`
- setgid enabled
- sticky bit enabled

**Commands:**

```bash
mkdir -p /shared/projects
chown root:developers /shared/projects
chmod u=rwx,g=rwx,o=rwx /shared/projects
chmod g+s /shared/projects
chmod +t /shared/projects
```

**Verify:**

```bash
ls -ld /shared/projects
```

**Expected:**

```text
drwxrwsrwt root developers /shared/projects
```

**Decoding `drwxrwsrwt`:**

```text
d    → directory
rwx  → owner = rwx
rws  → group = rwx + setgid enabled
rwt  → others = rwx + sticky bit enabled
```

---

## 41. Verification Commands

```bash
ls -l file                              # Check file
ls -ld directory                        # Check directory itself
id username                             # Check user
grep developers /etc/group              # Check group

# Verify setgid inheritance
touch /shared/projects/test.txt
ls -l /shared/projects/test.txt         # group should be "developers"
```

---

## 42. Important RHCSA Troubleshooting Habits

> When a task fails, don't immediately change random permissions.

**Check:**

```bash
ls -ld /path
ls -l /path/file
id username
```

**Ask:**

- Who owns the object?
- What is the group?
- What permissions does the user have?
- Does the user have execute permission on parent directories?
- Is setgid required?
- Is sticky bit required?
- Is the path correct?
- Did I verify the final state?

---

## 43. Common Mistakes

| # | Mistake | Fix / Note |
|---|---------|------------|
| 1 | Forgetting `-a` → `usermod -G developers devuser` | Use `usermod -aG developers devuser` |
| 2 | Wrong path (`/shared/project` vs `/shared/projects`) | Always carefully verify paths |
| 3 | Confusing setgid and sticky bit | setgid → inherit group; sticky → protect files from deletion/rename |
| 4 | Confusing `s` and `S` | `s` = special bit + execute; `S` = special bit + no execute |
| 5 | Confusing `t` and `T` | `t` = sticky + execute; `T` = sticky + no execute |
| 6 | Using `ls -l` instead of `ls -ld` on a directory | Use `ls -ld /shared/projects` to inspect the directory itself |

---

## 44. RHCSA Mental Model

When you receive a permissions task, think in this order:

1. What object? (file or directory?)
2. What owner?
3. What group?
4. What permissions? (u/g/o)
5. Special permission? (setuid? setgid? sticky?)
6. What exact path?
7. Configure it.
8. Verify it.

---

## 45. RHCSA Command Cheat Sheet

**Users**

```bash
useradd USER
usermod -s /bin/bash USER
passwd USER
```

**Groups**

```bash
groupadd GROUP
usermod -aG GROUP USER
id USER
```

**Ownership**

```bash
chown USER:GROUP FILE
chown :GROUP FILE
chown -R USER:GROUP DIRECTORY
```

**Permissions**

```bash
chmod 755 FILE
chmod u+x FILE
chmod g+r FILE
chmod o-r FILE
chmod u=rw,g=r,o= FILE
```

**setuid**

```bash
chmod u+s FILE
chmod u-s FILE
```

**setgid**

```bash
chmod g+s FILE
chmod g-s FILE
```

**Sticky bit**

```bash
chmod +t DIRECTORY
chmod -t DIRECTORY
```

**Verification**

```bash
ls -l FILE
ls -ld DIRECTORY
id USER
```

---

## 46. Practice Lab

> Use a disposable RHCSA practice VM.

### Lab 1 — User and Group
- Create user `devuser`, group `developers`
- Set shell to `/bin/bash`
- Add `devuser` → `developers`
- Verify using `id devuser`

### Lab 2 — Project Directory
- Create `/opt/project`
- Owner = `root`, Group = `developers`, Permissions = `770`
- Verify

### Lab 3 — Configuration File
- Create `/opt/project/config.txt`
- Owner = `root`, Group = `developers`, Permissions = `640`
- Verify

### Lab 4 — Script
- Create `/opt/project/test.sh`
- Owner = `rwx`, Group = `rx`, Others = `---` (use symbolic permissions)
- Expected: `-rwxr-x---`

### Lab 5 — setuid
- Create a safe test executable
- Practice: `chmod u+s FILE` → `ls -l FILE` → `chmod u-s FILE` → `ls -l FILE`
- Understand `s` vs `S`

### Lab 6 — setgid Directory
- Create `/shared/projects`, set group `developers`
- Enable setgid, create a file inside
- Verify the file inherits `developers` as its group

### Lab 7 — Sticky Bit
- Create `/shared`
- Enable `chmod +t /shared`
- Verify: `drwxrwxrwt`

### Lab 8 — Combined RHCSA Task
- Create `/shared/projects`
- Owner = `root`, Group = `developers`
- Owner/Group/Others = `rwx`
- setgid + sticky bit enabled
- Verify: `drwxrwsrwt root developers /shared/projects`

---

## 47. Self-Test Questions

> Do not look at the answers immediately.

1. What does `chmod 640 file` mean?
2. What does `chmod u+x file` do?
3. What is the difference between `chmod +t /shared` and `chmod g+s /shared`?
4. What does `-rwsr-xr-x` mean?
5. What does `-rwSr-xr-x` mean?
6. What does `-rwxr-sr-x` mean?
7. What does `-rwxr-Sr-x` mean?
8. What does `drwxrwxrwt` mean?
9. What does `drwxrwxrwT` mean?
10. Why does a setgid directory cause newly created files to inherit its group?
11. What is the difference between `ls -l /shared` and `ls -ld /shared`?
12. Why is `usermod -aG developers devuser` preferred over `usermod -G developers devuser` when adding a supplementary group?

---

## 48. Final Exam-Level Mental Cheat Sheet

```text
r = 4      u = owner     + = add
w = 2      g = group     - = remove
x = 1      o = others    = = exact

setuid:
  u+s → effective UID → appears in owner execute position

setgid executable:
  g+s → effective GID → appears in group execute position

setgid directory:
  g+s → new objects inherit directory group

sticky directory:
  +t → users generally cannot delete/rename other users' files

s = special + execute      t = sticky + execute
S = special + no execute   T = sticky + no execute
```

---

## 49. Mastery Checklist

- [ ] Create users
- [ ] Create groups
- [ ] Add users to supplementary groups
- [ ] Explain `-aG`
- [ ] Check users with `/etc/passwd`
- [ ] Check groups with `/etc/group`
- [ ] Use `id`
- [ ] Create directories with `mkdir`
- [ ] Understand `mkdir -p`
- [ ] Create files with `touch`
- [ ] Use `chown`
- [ ] Understand recursive `chown -R`
- [ ] Use `chmod`
- [ ] Understand numeric permissions
- [ ] Understand symbolic permissions
- [ ] Explain u/g/o
- [ ] Explain +/-/=
- [ ] Explain file rwx
- [ ] Explain directory rwx
- [ ] Configure setuid
- [ ] Explain real UID vs effective UID
- [ ] Recognize setuid `s` / `S`
- [ ] Configure setgid executable
- [ ] Configure setgid directory
- [ ] Explain group inheritance
- [ ] Recognize setgid `s` / `S`
- [ ] Configure sticky bit
- [ ] Recognize sticky `t` / `T`
- [ ] Configure shared directories
- [ ] Verify permissions with `ls -l`
- [ ] Verify directories with `ls -ld`
- [ ] Verify groups with `id`
- [ ] Troubleshoot permission problems systematically

---

## 🏆 Topic Status: Permissions & Ownership

**STATUS: MASTERED ✅**

**Topics covered:** Users · Groups · Ownership · `chmod` · `chown` · Numeric permissions · Symbolic permissions · File permissions · Directory permissions · setuid · setgid · Sticky bit · Shared directories · Permission verification · RHCSA troubleshooting

**Next Topic:** `UMASK`

Taught from fundamentals, then:
1. Level 1 → Basic understanding
2. Level 2 → Command practice
3. Level 3 → Troubleshooting
4. Level 4 → RHCSA-style tasks
5. Level 5 → Exam-level practical scenarios
