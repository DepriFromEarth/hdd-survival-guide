# hdd-survival-guide
A guide for people still on HDDs in the modern age where SSDs control the world!

Questions you should ask yourself before starting this guide

1. Do I have an SSD and HDD? If you do, then follow this video. https://www.youtube.com/watch?v=QG4LXw4Nd5U

2. Do I have multiple drives? If so, set them up as RAID. Don't use RAID if data protection is critical to you.

3. Do I have a traditional HDD? If so, continue with the rest of the guide!

4. Am I on Windows or Linux?

5. Is my hard drive dying? https://www.youtube.com/watch?v=OGOHA-t6j6M


## Where to Begin?
- If you are on Windows, proceed here.
https://github.com/DepriFromEarth/hdd-survival-guide/blob/main/README.md#windows
- If you are on Linux, proceed here.
https://github.com/DepriFromEarth/hdd-survival-guide/blob/main/README.md#linux

# Windows

## Partition Style
Before we start, we have to find out which type of BIOS we have so we can choose the proper partitioning style.
After we find this out, we can wipe the drive and install Windows again if we have the improper style.
If you are already using the proper style, you can skip this step.

- 1. Open "Run" with the Windows key + R, type in "msinfo32," and press enter.
- 2. click System summary to bring us to the correct page.
- 3. Find "BIOS Mode" It will say "Legacy" or "UEFI."

If it displays legacy, we will use the MBR partitioning style; if it displays UEFI, we will use GPT.

You can break these rules if your hard drive has 2 TB or more.

This will require you to use GPT regardless of whether you have a legacy BIOS because MBR simply doesn't support over 2 TB; the extra space will be lost.

- 4. Now type in Run, "diskmgmt.msc."
- 5. Find "Disk Drives" and collapse the section.
- 6. Right-click your disk and select properties. Then go to "Volumes."

Here we can see if our partitioning style is MBR or GPT. If everything matches with our BIOS mode, nothing needs to be done!

- If we have MBR but our BIOS is UEFI, I suggest you reinstall Windows with the GPT style for greater storage and speeds.
  
- If we have GPT but our BIOS is legacy and the drive is 2TB+, we don't have to do anything.

- Do not try to mix and match GPT and MBR partitioning styles with each other because issues can arise.

We can now close msinfo32 and devmgmt.

- There are multiple ways to reinstall Windows, so I will not go over that here, but I suggest using Rufus and selecting the right partition style, use NTFS then burning the desired iso to the USB.

Now that we have reinstalled Windows or simply checked that everything is okay, let's continue down below.

## Short Stroke
 We will short-stroke our hard drive to increase the minimum speeds of the drive.
- Please follow this video: https://www.youtube.com/watch?v=toLYV7th0L8
- Here's the official link to HDD Tune: http://www.hdtune.com/download.html

You want to keep your OS near the beginning of the drive (15-20GB Partition depending on your needs) and everything else on a separate partition.

## Programs to use
- TeraCopy: https://www.codesector.com/teracopy
I highly suggest using this program even if you have an SSD.
Instead of copying files all at once TeraCopy will copy one at a time to improve transfer speeds.
Also has an option to disable asynchronous if you disable smt/ht

- EverythingToolbar: (https://github.com/srwi/EverythingToolbar/releases)
Find any file instantly and extremtly better than windows explorer file search.

- 7zip: https://www.7-zip.org/
A better program for handling archive files.
    
 ## Enabling Prefetching and Font Cache and Disabling Windows' Heartbeat and Indexing
After that, we need to enable a couple services and set them to automatic.
By default, they should be like this, but just in case, we will enable them if they get disabled somehow.

- Open Run and type "services.msc." 
- Find "ReadyBoost", "Superfetch/Sysmain" and "Windows Font Cache Service" along with "Windows Presentation Foundation Font Cache 3.0.0.0".
- Right click on them select properties and set the startup type to automatic and start the services.
- Then click Apply and OK.

If you want to double check to ensure they are on goto [Computer\HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services] and find FontCache, FontCache3.0.0.0, Superfetch/SysMain and make sure the "Start" key is set to 3.

For the heartbeat goto [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Reliability] and set TimeStampInterval to 0.

Finally to disable indexing 
- Open Run once again and type "services.msc." 
- Find "Windows Search".
- Right click on it select properties and set the startup type to disable and stop the service.
- Then click Apply and OK.

## Write Buffer Cache
You can check if it's on by opening Run and typing "devmgmt.msc," looking for "Disk drives," collapsing the section, and right-clicking your desired drive and clicking properties, then going into policies to disable or enable it.

On for performance, Off for Data protection

"turning disk write caching on may increase operating system performance; however, it may also result in the loss of information if a power failure, equipment failure, or software failure occurs." - https://learn.microsoft.com/en-us/troubleshoot/windows-server/backup-and-storage/turn-disk-write-caching-on-off

## Task Scheduler and Startup Apps
In Task Scheduler, delete or disable all of the tasks.

- Open Run and type msconfig
- then go to Startup and click Disable All. (Windows 7, 8, XP, and Vista)

If you are on Windows 10+, you'll have to open up Task Manager and disable startups from there, or 
- press Windows key + I and go to Apps, 
- then Startup, and disable each app.

## File system optimization
Open CMD & enter the commands below.

- Disables the creation of 8.3 character-length file names on FAT- & NTFS-formatted volumes

    ```bat
    fsutil behavior set disable8dot3 1
    ```

- Disable updates to the Last Access Time stamp on each directory when directories are listed on an NTFS volume

    ```bat
    fsutil behavior set disablelastaccess 1
    ```

- Disables delete notifications (also known as trim or unmap).

    ```bat
    fsutil behavior set disabledeletenotify 1
    ```
- Enable compression ( Consider benchmarking on vs off with crystal disk info )

    ```bat
    fsutil behavior set disablecompression 0
    ```

## Optimizing file placement

Install the Windows Performance Tool Kit, then reboot your system. https://developer.microsoft.com/en-us/windows/downloads/sdk-archive/

Now open a command prompt with admin rights and type

    -xbootmgr -trace boot -prepSystem -verboseReadyBoot
    
Your PC will restart six times. After the second reboot, the MS defragmentation program is running and is placing the files into an optimized layout so that Windows will boot up faster. The last reboot was training for ReadyBoot. After the training is completed, you will notice a significant improvement in startup time. 

## Compare boot times

To see the improvement in time, run these 2 commands:

    xperf -i bootPrep_BASE+CSWITCH_1.etl -o 01_summary_start.xml -a boot
    xperf -i boot_BASE+CSWITCH_1.etl -o 02_summary_end.xml -a boot

To determine the boot time, open the XML files and look at the value bootDoneViaPostBoot. This value (-10000 = 10seconds) shows you the time, which Windows needs to boot completely.

In the file 02_summary_end.xml it should be much lower.

## Defrag
Now that we have done all that, we can discuss defragging programs.

- I can only suggest using Contig or the default Windows defragmenter.

  - Contig: https://docs.microsoft.com/en-us/sysinternals/downloads/contig

  - Contig Guide: https://en.wikipedia.org/wiki/Contig_(defragmentation_utility)
 
  - Contig GUI: https://www.majorgeeks.com/files/details/power_defragmenter_gui.html

# Linux

Most of this stuff is already documented, but I will go over it here.

## Partition Alignment

Aligning partitions correctly avoids excessive read-modify-write cycles. A typical practice for personal computers is to have each partition's start and size aligned to 1 MiB (1 048 576 bytes) marks. This covers all common page and block size scenarios, as it is divisible by all commonly used sizes—1 MiB, 512 KiB, 128 KiB, 4 KiB, and 512 B. - https://wiki.archlinux.org/title/Advanced_Format#Partition_alignment

## Swap

If you want speed, disable it. To deactivate specific swap space:

    swapoff /dev/sdxy
or

    swapoff /dev/sdxy -a

To deactivate all swap space.

Since swap is managed by systemd, it will be activated again on the next system startup. To disable the automatic activation of detected swap space permanently, run ``systemctl --type swap`` to find the responsible .swap unit and mask it.

or

open /etc/fstab file, search for the swap line and comment the entire line by adding a # (hashtag) sign in front of the line - https://www.tecmint.com/disable-swap-partition-in-centos-ubuntu/


Disabling, "can sometimes lead to a degradation, since it decreases the memory available for virtual file system (VFS) caches, causing more frequent and costly disk usage. - https://wiki.archlinux.org/title/Swap

So if you want to keep swap because of this let's first check our "swappiness"

    sysctl vm.swappiness

If it is not 10 let's change it to that this will keep our swappiness but not get rid of it in low memory cases.

Open up /etc/sysctl.conf with your desired text editor and type this at the end of the config.

    vm.swappiness = 10

## File Systems

Types of file systems: https://wiki.archlinux.org/title/File_systems

I will only go over EXT4 and XFS here because these are the only ones I think you should be using.

Type this command into a terminal to figure out with filesystem we are using.

    sudo blkid

- Disabling access time update. This works on both filesystems listed here

    With sudo privileges and your preferred text editor, open /etc/fstab and add "noatime" after default with a, (comma) symbol, as shown below. 

        /dev/sda5    /    ext4    defaults,noatime    0    1

- EXT4 

    - Install E4rat

        E4rat is a preload application designed for the ext4 filesystem. It monitors files opened during boot, optimizes their placement on the partition to improve access time, and preloads them at the very beginning of the boot process.

    - Defrag

        First, we want to check if we should even defrag.

        Open up a terminal and type

            sudo e4defrag -c /

        This will check your current mounted drive and show you which files are fragmented and if you need to defrag or not.

        To defrag EXT4, open a terminal and start typing;

            sudo e4defrag /

        This will defrag the entire drive.

- XFS

    - Defrag

        Type in a terminal;

            sudo xfs_db -r /dev/x

        Then,

            frag -d

        Then

            frag -f

        This will show us our fragmentation levels. After we are done checking them simply type quit to exit xfs_db. 

        Now for the defrag, simply type

            xfs_fsr


- More on defragging EXT4 and XFS here along with performance increases:

    - https://wiki.archlinux.org/title/XFS#Performance

    - https://www.rootusers.com/how-to-defragment-an-xfs-file-system/

    - https://man7.org/linux/man-pages/man8/e4defrag.8.html

    - https://wiki.archlinux.org/title/Ext4#Improving_performance
 
 ## Startup apps
 
 Show hidden startup apps:

    sudo sed -i "s/NoDisplay=true/NoDisplay=false/g" /etc/xdg/autostart/*.desktop
    
 Do not mindlessly disable things here. Disable what you think should be disabled, i.e., Bluetooth, if you don't use it.
 
 ## Clean Up

This entirely depends on your package manager but Bleachbit is an option to clean up junk files along with Sweeper and Stacer.

- https://www.bleachbit.org/download/linux
- https://apps.kde.org/sweeper/
- https://github.com/oguzhaninan/Stacer

# NCQ
Enter BIOS and search for the storage configuration tab. Find your hard drive and set the SATA controller to ACHI. It should be like this by default, but if it isn't, enable it. Doing so will enable NCQ and allow the physical header to optimize where files should go. https://en.wikipedia.org/wiki/Native_Command_Queuing

# Notes

When should I defrag? when the system is around 56% fragmented. Constantly defragging may prematurely kill your drive.
Avoid tapping the drive or a rumbly environment since this will affect the phsyical header.

# Credits
Thank you amitxv, Dato, Boga, MagicAndre1981, LinusTechTips, NCIX Tech Tips, Wiki, Microsoft and the Linux community

- Special thanks to Cynar for telling me to put this on github.
