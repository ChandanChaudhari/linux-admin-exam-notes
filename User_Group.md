#User

## User Add and del

useradd user1

passwd user1 (Then enter password) 

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


#ACL 

## Give user read access without changing Ownership to file 

setfacl -m u:user1:r /dev/file1 

## Give user to write access to dir ( to give write access need to give full rwx to dir)

setfacl -m u:user1:rwx /dir
