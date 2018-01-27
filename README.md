# oVIRT_Simple_Backup

### A REST API backup from BASH for oVirt 4.2.x

#### Outline

This script has the following functionality in a linux command line GUI:
 - Settings Manager to setup your environment and allow script to connect and backup/restore
 - Backup all selected VMs on a cron schedule to a specified folder or share
 - Backup a single VM from within the GUI
 - Restore a single VM from your backed up VMs in the GUI
 - Stop/Start VMs from within the GUI
 
#### Items Not Yet Completed
 - Better docblocks in code to make contributors possible ;)
 - Restore currently creates a clone which needs NICS, CPU, MEM, etc adjusted after complete. Working on getting a full restore to work but API is being fussy and cryptic.
 - **Sometimes a shutdown/start of the VM Backup Appliance running the script is required as oVirt sometimes cannot image the reused /dev/XdX. If you are having issue, shutdown the vm and then start it again. Reboot wont work.**
---

###### THIS SCRIPT IS CURRENTLY BETA and should only be used by those who understand the risks. 

---

#### Requirements

Create a VM in oVirt (Example: 20GB HDD (virtio), 8GB RAM, Debian8)

This VM will be used as the Backup_VM_Appliance and will be the manager for all backups

Backup_VM_Appliance
 - scsitools
 - curl
 - xmlstarlet
 - lsscsi
 - pv
 - dialog
 - sendmail for emailing logs/alerts
 - uuid-runtime (used to generate unique uuid)

#### Install

**On Backup_VM_Appliance**

 - Download the files backup.sh and src folder to your backup script directory
 
 - chmod +e backup.sh
  
 - create a mount to your NFS backup storage
    ```bash
    mkdir /mnt/backups
    
    #vi /etc/fstab 
    #add the following line with your IP and PATH info
 
    192.168.1.123:/path/to/folder/on/nfs /mnt/backups nfs rw,async,hard,intr,noexec 0 0
    ```
 - mount your backup folder<br>
    ```bash
    mount /mnt/backups
    ```

 - .\backup.sh 
    - [S]ettings -> configure for your environment
    - [0] Select your VMs to backup

 - Create a cron job to run your backups on a schedule using 
    ```
    /path/to/backup.sh --headless > /dev/null 2>&1
    ```

#### Menu / Functionality
 - [0] Select VMs to Backup (#of selected already)
   - shows a list of all VM names in cluster as well as their state (up/down)
   - allows select/deselect from saved list of VMs to backup on cron schedule
 - [1] Shutdown a VM
   - shows a list of all running VMs and allows shutdown of each
 - [2] Start a VM
   - shows a list of all stopped VMs and allows start of each
 - [3] Backup a Single VM
   - shows a list of all VM names in cluster and allows you to backup individually
 - [4] Restore a Single VM
   - shows a list of all backup folders on target backup location - named after each VM
   - VMs are not required to be in cluster for restore
   - selection of VM Folder -> UUID (allows multiple id) -> BU Folder Date
   - asks for new VM Name
   - creates a new VM with a new name and same sized Disk as original VM
   - Restored VM still requires adjusting NICS, RAM, etc in oVirt  
 - [5] Run Backup of (#of selected already) Selected VMs
   - same as running script in --headless mode
   - backups all targeted VMs
 - [S] Settings
   - pre-label to backup names (BU_)
   - uuid of the VM running the script (xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx)
   - FQDN of oVirt Engine (https://ovirtmgmt.mydomain.com)
   - oVirt Engine user (admin@internal)
   - oVirt Engine pass
   - backup  data path (/mnt/backups) *NO TRAILING /*
   - disk device type (sd/vd) vd recommended
   - disk type (virtio_scsi,virtio) virtio recommended
   - increment disk_devices (1/0) 0 recommended
   - backup log location (/var/log/ovirtbackup.log)
   - email for alerts from automated backup
   - retention period for number of backups to keep for each VM (2) 
   - restore storage domain name
   - restore cluster name


#### Author

You can reach zipur on the IRC SERVER irc.oftc.net CHANNEL #ovirt

http://zipur.ca

aka (Preston Lord)

