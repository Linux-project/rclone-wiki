**The goal of this wiki to show you how easy it is to enable VSS for rclone or any windows app.**
***
As per [Wikipedia](https://en.wikipedia.org/wiki/Shadow_Copy):
> Volume Snapshot Service or VSS is a technology included in Microsoft Windows that can create backup copies or snapshots of computer files or volumes, even when they are in use. A snapshot is a read-only point-in-time copy of the volume. Snapshots allow the creation of consistent backups of a volume, ensuring that the contents do not change and are not locked while the backup is being made.

I use rclone to copy very large files created by Veeam Backup and Replication which can take 24+ hours, depending on the size of the files and internet speeds. 

If Veeam backup software would run again while rclone is still uploading, Veeam would modify the set of files that rclone is still uploading, resulting in a corrupted and uselss set of backup files. Or if I wanted rclone to check a set of large sized files, Veeam might run again and modify the local files while rclone is still checking and generate errors in the log files.

So I create a VSS read-only point-in-time snapshot and have rclone use that as the source.
Now rclone can takes its time, upload, sync, check or whatever and not be concerned that the data will be modified.

Yeah, you want that, you know you need that, and you can have it with 3 lines of code.  

The following example is the bare minimum, just enough code to create a snapshot but not useful yet. 

1 - Create a file named vs.cmd, with 1 line of code.

    vshadow.exe -nw -script=setvar-vshadow.cmd -exec=exec.cmd c:

2 - Create a file named exec.cmd file with 2 lines of code.

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\

Execute vs.cmd and that is all it takes to create a shadow mount.
When vs.cmd is run, it will execute vshadow.exe.

vshadow.exe will do two things:
1. Create a file named setvar-vshadow.cmd will some variables that needs to be passed to exec.cmd
2. Execute exec.cmd

exec.cmd will do two things:
1. Call setvar-vshadow.cmd
2. Create a temporary shadow mount point. To be clear the shadow point will only exist will exec.cmd is running. When exec.cmd is done running, that mount point is no longer valid.

To make it useful for rclone, change exec.cmd to the 4 lines of code below and then execute vs.cmd.

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\
    rclone sync c:\snapshot\ dest:snapshot
    rmdir c:\snapshot\ /q

I find it confusing, that c:\snapshot\ is a mirror image of c:\.
I found that when writing more complex scripts, this confusion was leading to errors.
So I will give you 3 workarounds for clear code for exec.cmd.

--- Use SUBST command.

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\ 
    subst t: c:\
    rclone sync t:\snapshot\ dest:snapshot
    subst t: /delete
    rmdir c:\snapshot\ /q

--- Use NET USE command

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\ 
    net share snapshot=c:\
    rclone sync \\localhost\snapshot\ dest:snapshot
    net share snapshot /delete
    rmdir c:\snapshot\ /q

--- Create a new drive letter. This is what I do as this is the most reliable and least code needed.
Create a new drive and use that for the mount point.
Most Windows computer hard drives will not have free space to create a new drive. So shrink your c: drive by just 1GB and use that space to create new partition and name it the b: drive. Since the b: drive will only be used to mount the vss point, 1GB is plenty of space, in fact 1MB is enough space.

    call setvar-vshadow.cmd
    mklink /d b:\snapshot\ %shadow_device_1%\
    rclone sync b:\snapshot\ dest:thetestfolder
    rmdir b:\snapshot\ /q

Good luck and if you have any questions or comments, please do not hesitate to contact me.
Create a post at the forum and put the "VSS question" in the subject and I will answer.

Now for the boring details.
Vshadow.exe is part of the Window SDK which can be download free from Micro$oft at https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk. You will have to install the SDK and search for vshadow.exe. Tho the SDK is for windows 10, it runs fine on Windows Server 2019, including the free Windows Server Hyper-V edition, which I love!

As for more detail as to what vshadow.exe is doing, check out https://docs.microsoft.com/en-us/windows/win32/vss/vshadow-tool-examples
