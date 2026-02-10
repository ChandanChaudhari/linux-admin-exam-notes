There is a local logical volumes in your system, named with shrink and belong to VGSRV volume group, mount to the /shrink directory. 
The definition of size is 320 MB. Requirement: Reduce the logical volume to 220 MB without any loss of data.
The size is allowed between 200-260 MB after reducing.
# Shrink Logical Volume Without Data Loss

## Scenario
- Logical Volume Name: `shrink`
- Volume Group: `VGSRV`
- Mount Point: `/shrink`
- Original Size: 320 MB
- Target Size: 220 MB
- Allowed Range: 200–260 MB
- Requirement: No data loss

---

## Procedure

#Verify the logical volume and mount point:
`` `bash

### df -h /shrink
### lvdisplay /dev/VGSRV/shrink

##Unmount the filesystem:
unmount /shrink

##Check filesystem integrity (mandatory before shrinking):
e2fsck -f /dev/VGSRV/shrink

##Resize the filesystem to a safe size smaller than the target LV:
resize2fs /dev/VGSRV/shrink 210M

##Reduce the logical volume size:
lvreduce -L 220M /dev/VGSRV/shrink

##Resize the filesystem to occupy the full logical volume:
resize2fs /dev/VGSRV/shrink

##Mount the logical volume back:
mount /dev/VGSRV/shrink /shrink

##Verify final size and status:
df -h /shrink
lvdisplay /dev/VGSRV/shrink

##Result :
The logical volume shrink is successfully reduced from 320 MB to approximately 220 MB, which is within the allowed range (200–260 MB), with no data loss.

erify the logical volume and mount point using df -h /shrink and lvdisplay /dev/VGSRV/shrink to confirm the current size is 320 MB and it is mounted on /shrink. 
Unmount the filesystem to safely perform shrinking by running umount /shrink and confirm it is unmounted using mount | grep shrink (no output should appear). 
Perform a mandatory filesystem check to prevent data corruption by executing e2fsck -f /dev/VGSRV/shrink and wait for it to complete successfully. 
Resize the filesystem to a size smaller than the target logical volume (safe value 210 MB) using resize2fs /dev/VGSRV/shrink 210M.
After the filesystem is resized, reduce the logical volume to the required size by running lvreduce -L 220M /dev/VGSRV/shrink and confirm when prompted. 
Resize the filesystem again to occupy the full logical volume by executing resize2fs /dev/VGSRV/shrink. 
Mount the logical volume back to its original mount point using mount /dev/VGSRV/shrink /shrink. 
Finally, verify the new size and data integrity by running df -h /shrink and lvdisplay /dev/VGSRV/shrink, confirming the logical volume size is approximately 220 MB, which falls within the allowed range of 200–260 MB and that no data loss has occurred.
