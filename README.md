# borg.backup
Borg backup script to automate backups/restores using either a Hetzner Storage Box or a local backup, e.g. to an USB connected device

**The following distributions should be supported:**
* Ubuntu/Debian
* RHEL / AlmaLinux / Rocky Linux / CentOS
* Arch Linux
* openSUSE / SUSE Enterprise Server (SLES)
  
However, up to now it was only thoroughly tested on Ubuntu. 

**How it works:**
* It backups/restores strictly on a file level. This means, no partition table changes are done. The partitions must be prepared beforehand
* it does a "kamikaze" style restore, meaning replacing the current data by the one in the backup. This has some limitations but should work in most environments


🛑 **Before using this in production, test it well!** To be clear on that: ***You cannot hold us liable for any damage resulting from the use of this software! You will use it on your own risk!***

## **Installation:**
1. Copy the script and its config file to the server(s)
2. Make the borg.backup file executable
3. Change the borg.backup.config file to contain the respective credentials and also to fit your needs
4. Execute "borg.backup install". This will install all the necessary dependencies, and if needed, also initializes the respective Hetzner and borg storage.
5. If the script file is not already stored in the storage, it will also copy itself there to create a centralizzed auto-update repository for the script. Future script updates will be done exclusively on the storage as the script will self-update on every run. This keeps all your servers in-sync.

## **Updating:**
1. Edit or replace the file on the server or USB drive borg repo.
2. The script will auto-update the machine(s) on its next run.

## **Backup:**
1. Run "borg.backup backup"

## **Restore:**
1. First, if needed because your old install is not bootable anymore, install the **same minimal OS version** using the **same boot type (BIOS/UEFI)** using **the same partition layout** on the server as the backed up one had, and
2. then install the script as mentioned above.
3. Make sure you have enough local free space on the partition mounted on / as the data will temporarily be written there
4. Next, execute "borg.backup restore-live".
   * The script will try to automatically set the IP address of the current machine it is restored on
   * It will also try to adapt to different hardware (which is quite easy in Linux)
   * After restore and reboot, it will initiate an autoupdate for the OS, therebybfringing it's components up to date
   * You might wonder why ot also searches for a file /private-backup/var/clusterstatus. The reason is simple: In my setups it determines the current role of a cluster server. Depending on that, some more actions are taken. This very part is only executed in case it finds my encironment. If not, thos is "dead code" that will not be executed on your machine.

## **Constraints:**

On restore:
* When the backed up image has been BIOS based and is should be restored to a newly installed machine, the new minmal install must also be a BIOS based one. Same is true with UEFI.
* When the backed up image e.g. was Ubuntu 24.04, the minimal OS must also be of the same OS version. Also, they should follow the same partition layout as this script does **not** alter the partition tables.
* It rewrites the boot record. In case you have several boot options (like, a Windows partition or so), you will most likely have to restore these boot optoins after restore

These constraints have to do with the "kamikaze-style" restore. This type of restore was chosen because it's way more provider agnostic (every server provider [Hetzner/OVH...] has different recovery console mechanisms, so you would have to adapt it for each single provider) and often also simpler and faster. Also, this way you always start with a fresh OS instead of first having to deal with a broken or even compromised one.

You can exclude some additional custom directories from the backup as well as from the restore.

On smaller (likely virtual) servers, to prevent low memory conditions, during the backup a temporary swap file will be created which will be removed again after the backup.
