# hdd-survival-guide
A guide for people still on HDDs in the modern age where SSDs control the world!

## Questions you should ask yourself before starting this guide;

1. Do I have and SSD and HDD? If you do then follow this video. https://www.youtube.com/watch?v=QG4LXw4Nd5U

2. Do I have only 1 or more HDDs? If so continue to the rest of the guide!

3. Am I on Windows or Linux?

##If you have Windows procede here.

Before we start we have to find out which type of BIOS we have so we can choose the proper partitioning style.
After we find this out we can wipe the drive and install windows again if we have the improper style for whatever reason.
If you are already on the proper style you can skip this step.

Open Run with the windows key type in "msinfo32" and press enter. If not there already click on System summary so we are on the right page.
Find "BIOS Mode" it will say Legacy or UEFI. If it displays legacy we will use the MBR partitioning style if it displays UEFI we will use GPT.

You can break these rules if your hard drive have 2TiBs or more.
This will require you to use GPT reguardless if you have a legacy bios because MBR simply doesn't support over 2TiBs, the extra space will be lost.

Now type in Run "diskmgmt.msc". Right click your disk and select properties. Then goto "Volumes".
Here we can see if our partitsioning style is MBR or GPT. If everything matches with our BIOS Mode nothing needs to be done!
If we have MBR but our BIOS is UEFI I suggest you reinstall windows with the GPT style for greater storage and speeds.
If we have GPT but our BIOS is legacy and the drive is 2TiBs+ we don't have to do anything.

Do not try to mix and match GPT and MBR partitioning styles with each other because issues can accure.

There are multiple ways to reinstall Windows so I will not go over that here but I suggest using Rufus and selecting the right partition style then burn the desired iso to the usb.

Now that we have reinstalled windows or simply checked everything is okay we will short stroke our hard drive to increase the minimum speeds of the drive.
Please follow this video: https://www.youtube.com/watch?v=toLYV7th0L8
And this is a link for HDD Tune: http://www.hdtune.com/download.html

Install the Windows Performance Tool Kit then reboot your system. https://developer.microsoft.com/en-us/windows/downloads/sdk-archive/

After that we need to enable a couple services are running and set to automatic.
By default they should be like this but just in case we will enable them if they got disabled somehow.

1. Open Run once again and type "services.msc" Find Superfetch or Sysmain on Windows10+.
Set the startup type to automatic and start the services.
Then click Apply and OK.

2. Type in run "regedit".
Goto [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Session Manager\Memory Management\PrefetchParameters]
Find "EnablePrefetcher" and "EnableSuperfetch" and set their values to 3.

Now open a command prompt with admin rights and type
xbootmgr -trace boot -prepSystem -verboseReadyBoot

DON'T USE OTHER DEFRAGMENTATION PROGRAMS AFTER THE OPTIMIZATION, USE ONLY THE INCLUDED MS TOOL, BECAUSE EVERY TOOL PLACES THE FILES AT A DIFFERENT OFFSET ON YOUR HDD, BECAUSE ALL TOOL THINK THEY KNOW IT BETTER!

I suggest leaving write-buffer cache on and it should be by default but if you are worried about data loss its completely fine to disable it but speeds may suffer. You can check if it on by opening Run and typing "devmgmt.msc" look for "Disk drives" collapse the section and right click your desired drive then goto policies to disable or enable it.

Now we will optimize the file system. I will being using changes suggest by amitxv

Open CMD & enter the commands below.

Disables the creation of 8.3 character-length file names on FAT- & NTFS-formatted volumes

fsutil behavior set disable8dot3 1
Disable updates to the Last Access Time stamp on each directory when directories are listed on an NTFS volume

fsutil behavior set disablelastaccess 1
Enables delete notifications (also known as trim or unmap), should be enabled by default but here for safe measure

fsutil behavior set disabledeletenotify 0

I personally think you should disable Event Trace Sessions but if you need them for logging

Create registry files to toggle event trace sessions. Programs that rely on event tracers such will not be able to log data until the required sessions are restored which is the purpose of creating two registry files to toggle between them (identical concept to the service scripts). Open CMD and enter the commands below to build the registry files in the C:\ directory. As with the services scripts, these registry files must be ran with NSudo.

reg export "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\WMI\Autologger" "C:\ets-enable.reg"
>> "C:\ets-disable.reg" echo Windows Registry Editor Version 5.00
>> "C:\ets-disable.reg" echo.
>> "C:\ets-disable.reg" echo [-HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\WMI\Autologger]

Now we will look at cleaning junk files or unwanted programs.

Programs I suggest for cleaning up junk are;
Bleachbit (FOSS)
Cleanmgr+ (FOSS)
BCUninstaller (FOSS)
RevoUninstaller
Autoruns
I suggest using the portable versions of these programs.

If you do not wish to use a program for whatever reason and just use the Windows tools to clean your system continue here;

Some locations you may want to review for leftover bloatware and unwanted shortcuts 

"C:\"
"C:\ProgramData\Microsoft\Windows\Start Menu\Programs"
"C:\Program Files"
"C:\ProgramData"
"C:\Windows\SoftwareDistribution\download"
"C:\Windows\Temp"
"%userprofile%\AppData"
"%userprofile%\AppData\Local\Temp"
"%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs"
"%userprofile%\Downloads"

or

Open CMD and the command below to open all folders listed above at once
for %a in ("C:\", "C:\ProgramData\Microsoft\Windows\Start Menu\Programs", "C:\Program Files", "C:\ProgramData", "C:\Windows\SoftwareDistribution\download", "C:\Windows\Temp", "%userprofile%\AppData", "%userprofile%\AppData\Local\Temp", "%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs", "%userprofile%\Downloads") do (explorer %a)
Configure Disk Cleanup

Open CMD and enter the command below, tick all of the boxes, press OK

cleanmgr /sageset:50
Run Disk Cleanup

cleanmgr /sagerun:50

Then open Task Scheduler and delete or disable all the tasks in there.

Open Run and type msconfig then goto startup and click disable all. (Windows 7, 8, XP and Vista)
If you are on Windows 10+ you'll have to open up taskmanager and disable startups from there or press Windows key + I and goto Apps then startup and disable each app.

Now that we have done all that we can discuss Defragging programs 

Do not use;
O&O Defrag
UltraDefrag
Defraggler
Auslogics Disk Defrag
Puran Defragmenter
My Defragmenter
IOBit Smart Defrag
Glarysoft Disk SpeedUp
Toolwiz Smart Defrag
Wise Care 365
Systweak Advanced System
Fragger
PerfectDisk Pro
UltimateDisk Defrag
Condusiv Diskeeper
Baidu PC Faster for Windows
MyDefrag
VoptXP
etc.

I can only suggest using Contig or the default windows defraggermenter
https://docs.microsoft.com/en-us/sysinternals/downloads/contig
https://en.wikipedia.org/wiki/Contig_(defragmentation_utility)
https://www.majorgeeks.com/files/details/power_defragmenter_gui.html

When Should I defrag? When the system is around 56% fraggmentation. Constantly defragging may prematurely kill your drive.
Avoid tapping the drive or a rumbly environment since this will affect the phsyical header.

A video to check if your hard drive is dying: https://www.youtube.com/watch?v=OGOHA-t6j6M

A Linux Section will be added later

Now lets boot into BIOS and look for storage configuration. Find your hard drive and set the SATA controller to ACHI. It should be like this by default but if it isn't enable it doing so will enable NCQ and allows the physical header to optimize where files should go. https://en.wikipedia.org/wiki/Native_Command_Queuing
