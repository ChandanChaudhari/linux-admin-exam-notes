#User

## User Add and del

useradd user1

passwd user1 (Then enter password for user1) 

## delete existing user

userdel user1 (Delete user1 but keep home dir)

userdel -r user1 (delete user with there home directory)

# Home Dir 
## create user without home dir

useradd -M user1

## create user with custom home directory

useradd -d /data/user1 -m user1 

## change home dir of existing user

usermod -d /newhome/user1 -m user1

## Check user creation

id user1 (show user info) or which user1 

## Add user with specific UID

useradd -u 1234 user2

## Add user with specific uid with bash shell

useradd -u 2000 -m -s /bin/bash user1

## Create user with nologin shell

useradd -s /sbin/nologin user2

## Create system user (service account)

useradd -r serviceuser

## Add user with primary group

useradd -g database user3 

## Add user with primary and seconday groups 

useradd -g database -G wheel,devops user3 (create user with both primary and secondary groups) 

### p = primary group G = secondary(supplementry group)

## Add a existing user to additional group ( a= append , update group without removing previous group)

usermod -aG devops user3

## remove user from supplementry group ( additional group ) ( we cannot remove user primary group. If want to do first change user group to other group and delete previous group)

sudo gpasswd -d wheel user3 (Secondary group wheel will be deleted )
 
## change user primary group 

sudo usermod -g datastore user3

# Group
## create group with name database

groupadd database

## create group with specific GID

groupadd -g 3000 devops

## Check group exist or created

sudo groups user3

## delete group

groupdel devops

## Change name of existing group

groupmod -n newgroup oldgroup 

## remove user from group

gpasswd -d user1 group1

# Password and Account control

## set password for existing user

passwd user1

## lock user

passwd -l user1

## unlock user account

passwd -u user1 

## Disable user account without deleting account

usermod -s /sbin/nologin user1 

# chage

## check user aging policy

chage -l user1

## force user to change password at next login

chage -d 0 user1 

## Set password expiration to Max 30 days 

chage -M 30 user1

## set password warning period 

chage -W 5 user1

## Set Account expiry 

chage -E 2026-03-30 user1 


#  ACL 

## Give user read access without changing Ownership to file 

setfacl -m u:user1:r /dev/file1 

## Give user to write access to dir ( to give write access need to give full rwx to dir)

setfacl -m u:user1:rwx /dir

## remove acl permission from dir

setfacl -x u:user1 /dir

## vrify acl permission 

getfacl /directory

## Identify ACL from ls -l output (+ sign)

# Special permissions 

## The SGID (Set Group ID) bit on a directory ensures that new files and subdirectories created inside inherit the directory’s group ownership.

chmod g+s /path/to/dir   (ls -ld /path/to/dir this will show set guid drwxr-sr-x)

## Set sticky bit on a shared directory
## The sticky bit prevents users from deleting files they don’t own in a shared directory (commonly used on /tmp).

chmod +t /tmp (drwxr--rwt)

### (+t means other have xecute permission on dir )
### (+T means other dont have xecute permission)

## Only the file owner, the directory owner, or root can delete files.
## Other users, even if they have write permissions on the directory, cannot delete files they don’t own.

## . Identify SUID file
## The SUID (Set User ID) bit on a file allows the file to run with the owner’s privileges (often root), regardless of who executes it.

find / -perm -4000

# Umask
## check default umask

umask 

## What is umask?

### umask (User Mask) defines which permission bits should be turned off when new files or directories are created.
### It works by subtracting permissions from the system defaults:

### Default for files: 666 (rw-rw-rw-) → no execute by default.
### Default for directories: 777 (rwxrwxrwx).

## ✅ How umask works

## Formula:
### Final Permission = Default Permission - umask

## Example:

### umask = 0022

### Files: 666 - 022 = 644 → rw-r--r--
### Directories: 777 - 022 = 755 → rwxr-xr-x

## ✅ Why files don’t get execute by default

## For security reasons, files are created with 666 (no execute bit).
## Even if umask is 0000, files will be 666 → rw-rw-rw- (still no execute).
## To make files executable, you must manually add execute using chmod or create them via a program that sets execute.

Example:
If umask = 0027:

### Files: 666 - 027 = 640 → rw-r-----
### Directories: 777 - 027 = 750 → rwxr-x---

## Umask only removes permissions, never adds

### umask works like a filter: it subtracts permissions from the default set.
### It cannot grant extra permissions (like execute) that weren’t in the default.
### Example:

### Default file permissions: 666 (rw-rw-rw-)
### umask: 0022
### Result: 644 (rw-r--r--)

### Notice: umask removed write for group and others, but didn’t add anything.

## Set permanent umask for user

Edit the user’s shell configuration file /home/user1 (e.g., ~/.bashrc, ~/.profile, or ~/.bash_profile)
echo "umask 027" >> /home/user1/.bashrc

