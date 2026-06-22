# borg.backup
Borg backup script to automate backups/restores using either a Hetzner Storage Box or a local backup, e.g. to an USB connected device

**The following distributions should be supported:**
* Ubuntu/Debian
* RHEL / AlmaLinux / Rocky Linux / CentOS
* Arch Linux
* openSUSE / SUSE Enterprise Server (SLES)
  
However, up to now it was only thoroughly tested on Ubuntu. 

**Before using this in production, test it well!** To be clear on that: **You cannot hold us liable for any damage resulting from the use of this software! You will use it on your own risk!**

## **Installation:**
1. Copy the script and its config file to the server(s)
2. Make the borg.backup file executable
3. Change the borg.backup.config file to contain the respective credentials and also to fit your needs
4. Execute "borg.backup install". This will install all the necessary dependencies, and if needed, also initializes the respective Hetzner and borg storage.
5. If the script file is not already stored in the storage, it will also copy itself there to create a centralizzed auto-update repository for the script. Future script updates will be done exclusively on the storage as the script will self-update on every run. This keeps all your servers in-sync.

## **As for the backup:**
1. Run "borg.backup backup"

## **As for the restore:**
1. First, install the same minimal OS version using the same boot type (BIOS/UEFI) on the server as the backed up one was, and
2. then install the script as mentioned above.
3. Next, execute "borg.backup restore-live". 

## **Constraints:**

On restore, when the backed up image has been BIOS based, the new minmal install must also be a BIOS based one. When the backed up image was Ubuntu 24.04, the minimal OS must also be of the same OS version. 

These constraints have to do with the "kamikaze-style" restore. This type of restore was chosen because it's way more provider agnostic (every server provider [Hetzner/OVH...] has different recovery console mechanisms, so you would have to adapt it for each single provider) and often also simpler and faster. Also, this way you always start with a fresh OS instead of first having to deal with a broken or even compromised one.

You can exclude some additional custom directories from the backup as well as from the restore.
