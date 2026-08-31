# UMASK — RHCSA Quick Notes

- **UMASK** controls the default permissions of newly created files and directories.
- UMASK **removes permission bits**; it does not directly define final permissions.

- Regular file base permission: **666 (`rw-rw-rw-`)**
- Directory base permission: **777 (`rwxrwxrwx`)**

- UMASK applies only when an object is **created**; it does not change existing permissions.

- `umask` → displays UMASK in numeric/octal form.
- `umask -S` → displays UMASK in symbolic form.
- `umask 027` → changes UMASK for the current shell/session.

- Common results:
  - `022` → file `644`, directory `755`
  - `027` → file `640`, directory `750`
  - `077` → file `600`, directory `700`
  
- UMASK is a **permission-bit mask**, so don't treat it as simple decimal subtraction.
- `chmod` changes permissions of existing files/directories; `umask` controls default permissions at creation.

- User-specific persistent Bash settings can commonly be configured in `~/.bashrc`.
- System-wide shell configuration can involve `/etc/profile` and `/etc/bashrc`.

- `/etc/login.defs` may contain a default `UMASK` for login/account-related behavior.

## 2. Default Permissions

Linux starts with different maximum permissions for files and directories:

| Object | Base Permission |
|--------|-----------------|
| Regular file | `666` → `rw-rw-rw-` |
| Directory | `777` → `rwxrwxrwx` |

> Regular files normally do not receive execute permission when created.

---

## 3. Permission Values

| Permission | Value |
|------------|-------|
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute (`x`) | 1 |

Examples:

```text
7 = rwx
6 = rw-
5 = r-x
4 = r--
3 = -wx
2 = -w-
1 = --x
0 = ---
```
## 4. How UMASK Works

### UMASK specifies which permission bits should be removed.

### For example:

```
UMASK = 027

User   → 0 → remove nothing
Group  → 2 → remove write
Other  → 7 → remove read, write and execute

Base permissions: 666
UMASK:            027
Final permissions: 640

Result 
640 = rw-r-----

```
## Directory example
```
Base permissions: 777
UMASK:            027
Final permissions: 750

Result -
750 = rwxr-x---
```

## umask 035

## You answered:
`
file → 622 ❌
dir  → 742 ❌
`
## Remember: UMASK removes bits, it isn't normal decimal subtraction.

For the file:
`
666 = rw-rw-rw-
035 = ---rwx-rwx
`
## Remove the masked permissions:
`
User   rw- → rw-
Group  rw- → r--
Other  rw- → ---
`
### Therefore:
`
file → 640 = rw-r-----
`
### For the directory:
`
777 = rwxrwxrwx
035 = ---rwxrwx
`
## Result:

`
dir → 740 = rwxr-----
`













