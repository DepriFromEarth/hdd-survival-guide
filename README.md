# hdd-survival-guide
A guide for people still on HDDs in the modern age where SSDs control the world!

Questions you should ask yourself before starting this guide

1. Do I have an SSD and HDD? If you do, then follow this video. https://www.youtube.com/watch?v=QG4LXw4Nd5U

2. Do I have multiple drives? If so, set them up as RAID. ( I will add this to the guide later )

3. Do I have a traditional HDD? If so, continue with the rest of the guide!

4. Am I on Windows or Linux?


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

- Open "Run" with the Windows key, type in "msinfo32," and press enter. If we aren't there already, click System summary to bring us to the correct page.
Find "BIOS Mode" It will say "Legacy" or "UEFI." If it displays legacy, we will use the MBR partitioning style; if it displays UEFI, we will use GPT.

- You can break these rules if your hard drive has 2 TB or more.
- This will require you to use GPT regardless of whether you have a legacy BIOS because MBR simply doesn't support over 2 TB; the extra space will be lost.

    - Now type in Run, "diskmgmt.msc." Right-click your disk and select properties. Then go to "Volumes."
    - Here we can see if our partitioning style is MBR or GPT. If everything matches with our BIOS mode, nothing needs to be done!
    - If we have MBR but our BIOS is UEFI, I suggest you reinstall Windows with the GPT style for greater storage and speeds.
    - If we have GPT but our BIOS is legacy and the drive is 2TB+, we don't have to do anything.

- Do not try to mix and match GPT and MBR partitioning styles with each other because issues can arise.

- There are multiple ways to reinstall Windows, so I will not go over that here, but I suggest using Rufus and selecting the right partition style, then burning the desired iso to the USB.

## Short Stroke
Now that we have reinstalled Windows or simply checked that everything is okay, we will short-stroke our hard drive to increase the minimum speeds of the drive.
- Please follow this video: https://www.youtube.com/watch?v=toLYV7th0L8
- Here's the official link to HDD Tune: http://www.hdtune.com/download.html

You want to keep your OS near the beginning of the drive (15-20GB Partition depending on your needs) and everything else on a separate partition.

## Enabling Prefetching
Install the Windows Performance Tool Kit, then reboot your system. https://developer.microsoft.com/en-us/windows/downloads/sdk-archive/

After that, we need to enable a couple services and set them to automatic.
By default, they should be like this, but just in case, we will enable them if they get disabled somehow.

1. Open Run once again and type "services.msc." Find Superfetch or Sysmain on Windows 10 and later.
Set the startup type to automatic and start the services.
Then click Apply and OK.

2. Type "regedit" and press Enter.
Navigate to [HKEY_LOCAL_MACHINESYSTEMControlSet001ControlSession ManagerMemory ManagementPrefetchParameters] and then to [HKEY_LOCAL_MACHINESYSTEMControlSet001ControlSession ManagerMe].
Find "EnablePrefetcher" and "EnableSuperfetch" and set their values to 3.

Now open a command prompt with admin rights and type
```-xbootmgr -trace boot -prepSystem -verboseReadyBoot```

Now your PC will have been restarted six times. After the second reboot, the MS defragmentation program is running and is placing the files into an optimized layout so that Windows will boot up faster (for the description, read what ReadyBoot is). The last reboots were training for ReadyBoot. After the training is finished, you'll notice a huge improvement in startup.

Use only the included MS tool after the optimization because every tool places the files at a different offset on your HDD and all tools think they know it better!

## Write Buffer Cache
I suggest leaving the write-buffer cache on, and it should be by default, but if you are worried about data loss, it's completely fine to disable it, but speeds may suffer. You can check if it's on by opening Run and typing "devmgmt.msc," looking for "Disk drives," collapsing the section, and right-clicking your desired drive, then going into policies to disable or enable it.

## File System
Now we will optimize the file system.

Open CMD & enter the commands below.

- Disables the creation of 8.3 character-length file names on FAT- & NTFS-formatted volumes

    ```bat
    fsutil behavior set disable8dot3 1
    ```

- Disable updates to the Last Access Time stamp on each directory when directories are listed on an NTFS volume

    ```bat
    fsutil behavior set disablelastaccess 1
    ```

- Enables delete notifications (also known as trim or unmap), should be enabled by default but here for safe measure

    ```bat
    fsutil behavior set disabledeletenotify 0
    ```
    
## Configure Event Trace Sessions
I personally think you should disable Event Trace Sessions, but if you need them for logging

Create registry files to toggle event trace sessions. Programs that rely on event tracers such will not be able to log data until the required sessions are restored which is the purpose of creating two registry files to toggle between them (identical concept to the service scripts). Open CMD and enter the commands below to build the registry files in the ``C:\`` directory. As with the services scripts, these registry files must be ran with NSudo.

```bat
reg export "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\WMI\Autologger" "C:\ets-enable.reg"
>> "C:\ets-disable.reg" echo Windows Registry Editor Version 5.00
>> "C:\ets-disable.reg" echo.
>> "C:\ets-disable.reg" echo [-HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\WMI\Autologger]
```
## Clean Up
Now we will look at cleaning up junk files or unwanted programs.

I recommend the following programs for junk removal:
- Bleachbit (FOSS)
- Cleanmgr+ (FOSS)
- BCUninstaller (FOSS)
- RevoUninstaller
- Autoruns

I suggest using the portable versions of these programs.

If you do not wish to use a program for whatever reason and just use the Windows tools to clean your system, continue below.

## Cleanup

- Open Autoruns and remove any unwanted programs such as game launchers. Remove all obsolete entries with a yellow label, run with NSudo if you encounter any permission errors

- Some locations you may want to review for leftover bloatware and unwanted shortcuts

    - ``"C:\"``
    - ``"C:\ProgramData\Microsoft\Windows\Start Menu\Programs"``
    - ``"C:\Program Files"``
    - ``"C:\ProgramData"``
    - ``"C:\Windows\Prefetch"``
    - ``"C:\Windows\SoftwareDistribution\download"``
    - ``"C:\Windows\Temp"``
    - ``"%userprofile%\AppData"``
    - ``"%userprofile%\AppData\Local\Temp"``
    - ``"%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs"``
    - ``"%userprofile%\Downloads"``

    OR

    - Open CMD and the command below to open all folders listed above at once

        ```bat
        for %a in ("C:\", "C:\ProgramData\Microsoft\Windows\Start Menu\Programs", "C:\Program Files", "C:\ProgramData", "C:\Windows\Prefetch", "C:\Windows\SoftwareDistribution\download", "C:\Windows\Temp", "%userprofile%\AppData", "%userprofile%\AppData\Local\Temp", "%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs", "%userprofile%\Downloads") do (explorer %a)
        ```

- Clear the PATH user environment variable of locations pointing to Windows bloatware folders

- Configure Disk Cleanup

    - Open CMD and enter the command below, tick all of the boxes, press **OK**

        ```bat
        cleanmgr /sageset:50
        ```
    - Run Disk Cleanup

        ```bat
        cleanmgr /sagerun:50
        ```

## Task Scheduler and Startup Apps ( Boot Times )
In Task Scheduler, delete or disable all of the tasks.

Open Run and type msconfig, then go to Startup and click Disable All. (Windows 7, 8, XP, and Vista)
If you are on Windows 10+, you'll have to open up Task Manager and disable startups from there, or press Windows key + I and go to Apps, then Startup, and disable each app. This will help decrease the boot time.

## Defrag
Now that we have done all that, we can discuss defragging programs.

- I can only suggest using Contig or the default Windows defragmenter.

  - Contig: https://docs.microsoft.com/en-us/sysinternals/downloads/contig

  - Contig Guide: https://en.wikipedia.org/wiki/Contig_(defragmentation_utility)
 
  - Contig GUI: https://www.majorgeeks.com/files/details/power_defragmenter_gui.html

## TeraCopy
https://www.codesector.com/teracopy

I highly suggest using this program even if you have an SSD.
Instead of copying files all at once TeraCopy will copy one at a time to improve transfer speeds.

## Check if your Hard Drive is dying

Your Hard Drive Could be DYING. Here's How to Check!: https://www.youtube.com/watch?v=OGOHA-t6j6M

# Linux

## Partition Alignment

https://wiki.archlinux.org/title/Advanced_Format#Partition_alignment

## Swap

If you want speed, disable it. To deactivate specific swap space:

    swapoff /dev/sdxy
or

    swapoff /dev/sdxy -a

switch to deactivate all swap space.

Since swap is managed by systemd, it will be activated again on the next system startup. To disable the automatic activation of detected swap space permanently, run ``systemctl --type swap`` to find the responsible .swap unit and mask it.

Disabling, "can sometimes lead to a degradation, since it decreases the memory available for virtual file system (VFS) caches, causing more frequent and costly disk usage. - https://wiki.archlinux.org/title/Swap

So if you want to keep swap because of this let's first check our "swappiness"



## File Systems

Types of file systems: https://wiki.archlinux.org/title/File_systems

I will only go over EXT4 and XFS here because these are the only ones I think you should be using.

- EXT4 Defrag

    First, we want to check if we should even defrag.

    Open up a terminal and type

        sudo e4defrag -c /

    This will check your current mounted drive and show you which files are fragmented and if you need to defrag or not.

    To defrag EXT4, open a terminal and start typing;

        sudo e4defrag /

    This will defrag the entire drive.

- XFS Defrag

    Type in a terminal;

        xfs_db -r /dev/xvda1

    Then,

        frag -d

    Then,

        frag -f

    This will show us our fragmentation levels. After we are done checking them simply type quit to exit xfs_db. 

    Now for the defrag, simply type

        xfs_fsr

- More on defragging EXT4 and XFS here along with performance increases:

    - https://wiki.archlinux.org/title/XFS#Performance

    - https://www.rootusers.com/how-to-defragment-an-xfs-file-system/

    - https://man7.org/linux/man-pages/man8/e4defrag.8.html

    - https://wiki.archlinux.org/title/Ext4#Improving_performance

## NCQ
Enter BIOS and search for the storage configuration tab. Find your hard drive and set the SATA controller to ACHI. It should be like this by default, but if it isn't, enable it. Doing so will enable NCQ and allow the physical header to optimize where files should go. https://en.wikipedia.org/wiki/Native_Command_Queuing

## Notes

When should I defrag? when the system is around 56% fragmented. Constantly defragging may prematurely kill your drive.
Avoid tapping the drive or a rumbly environment since this will affect the phsyical header.

## Credits
Thank you amitxv, MagicAndre1981, LinusTechTips, NCIX Tech Tips, Wiki, Microsoft and the Linux community

- Special thanks to Cynar for telling me to put this on github.
