### XFS filesystem cannot be reduce only be extended. ext filesystem can be reduced as well as extended
#### e2fsck -f /dev/vg/lv verify fs integrity for ext3 and ext4 Mandatory before resizing or reducing. XFS cannot be reduced, so this step is irrelevant for shrinking XFS

Create a logical volume named database in the datastore volume group using 50 extents.
The PE size of the volume group is 16 MB.
Format it with ext3 and mount it persistently on /mnt/database.

#### 1) create volume group with PE=16M each.

`vgcreate -s 16 datastore /dev/sdb1`

#### 2) create logical volume with 50 extents

`lvcreate -n database -l 50 datastore`

#### 3) format filesystem 

`mkfs.ext3 /dev/datastore/database `

#### 4) mount filesystem to /mnt/database

`sudo mount /dev/datastore/database /mnt/database`

#### 5) Update entry to /etc/fstab (always update fs to fstab when newly created )

`echo "/dev/datastore/database /mnt/database ext3 defaults 0 0" >> /etc/fstab`
`mount -a `



#### The logical volume shrink in volume group datastore is mounted on /shrink and formatted with ext4.Reduce the size to 220 MB.
(The acceptable range is 200–260 MB.)

#### 1) unmount the lv

`umount /shrink`

#### 2) check file integrity

`e2fsck -f /dev/datastore/shrink`   (here shrink is lv)

#### 3) resize filesystem such that usable space is under limit of 200 to 260 

`resize2fs /dev/datastore/shrink 230M`  (always take some extra space for resizing) (resize2fs make filesystem to exacly new given size without couroupting any blocks or data)

#### 3) reduce lv size to 220MB 

`lvreduce -L 230 /dev/datastore/shrink`   (next step is exactly be mount filesytem. again no further step)

#### 4) check size of lV

`sudo lvs or lvdisplay`


<center> ### Extend the lv size () </center>

#### 1) find out the type of filesystem

`df -hT`

#### 2) extend the file lv

`sudo lvextend -l 40 /dev/datastore/database ` or ` sudo lvextend -L 400M /dev/datastore/database`

#### or #### lvextend -r -l 40 /dev/datastore/database or lvextend -r -L 400M /dev/datastore/database 

#### 3) extend the filesystem

`sudo resize2fs /dev/datastore/database` (if filesystem is ext4 ) or xfs_growfs /dev/datastore/database ( if filesystem is xfs )

#### 4) check updated size 

`df -hT`







