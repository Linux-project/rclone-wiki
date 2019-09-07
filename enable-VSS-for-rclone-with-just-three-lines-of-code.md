**The goal of this wiki to show you how easy it is to enable VSS for rclone or any windows app.**
***
As per Wikipedia:
“Volume Snapshot Service or VSS is a technology included in Microsoft Windows that can create backup copies or snapshots of computer files or volumes, even when they are in use. A snapshot is a read-only point-in-time copy of the volume. Snapshots allow the creation of consistent backups of a volume, ensuring that the contents do not change and are not locked while the backup is being made. “

I use rclone to copy very large files created by Veeam Backup and Replication which can take 24+ hours, depending on the size of the files and internet speeds. If Veeam software would run again, it would change the backup files before rclone is done uploading the pre-existing files. Or if I wanted rclone to check a set of large sized files, Veeam might run again and modify the local files while rclone is still checking and generate errors in the log files.
So I create a VSS read-only point-in-time snapshot and have rclone to that as the source.
now rclone can takes its time, upload, sync, check or whatever and not be concerned that the snapshot will be modified.

Yeah, you want that, you know you need that, and you can have it with three lines of code.  

This example is the bare minimum, just enough code to create that snapshot but not useful yet. 
1. Create a file named vs.cmd, with one line of code.

    vshadow.exe -nw -script=setvar-vshadow.cmd -exec=exec.cmd c:

2. Create a file named exec.cmd file with two lines of code.

**    call setvar-vshadow.cmd**
**    mklink /d c:\vs\ %shadow_device_1%\**

Execute vs.cmd and that is all it takes to create shadow mount.
When vs.cmd is run, it will execute vshadow.exe.

vshadow.exe will do two things:
1. Create a file named setvar-vshadow.cmd will some variables. 
2. Execute exec.cmd

exec.cmd will do two things:
1. Call setvar-vshadow.cmd
2. Create a temporary shadow mount point. To be clear the shadow point will only exist will exec.cmd is running. When exec.cmd is done running, that mount point is no longer valid.
That above code is the bare minimum, just to show you how easy it is to create a VSS.
 

To make it useful for rclone, lets change exec.cmd then execute vs.cmd.
call setvar-vshadow.cmd
mklink /d c:\vs\ %shadow_device_1%\
rclone sync c:\vs\users\%username%\downloads\ wasabiwest01:users\%username%\downloads
rmdir c:\vs\ /q

It can be confusing, that c:\vs\ is a mirror image of c:\.
I suggest that you create a new drive, I use B:\ and use that for the mount point.\
Most Windows computer hard drives will not have free space to create a new drive. So shrink your c: drive by just 1GB and use that space to create the b:\. Since the b:\ will only be used to mount the vss point, 1GB is plenty of space, in fact 1MB is enough space.
And now change exec.cmd code to be and execute vs.cmd.
This will do the same as the above code, but using the b: drive for the mount is less confusing.
call setvar-vshadow.cmd
mklink /d b:\vs\ %shadow_device_1%\ 
rclone sync b:\vs\users\%username%\downloads\ wasabiwest01:users\%username%\downloads
rmdir b:\vs\ /q

Now for the boring details.
Vshadow.exe is part of the Window SDK which can be download free from Micro$oft at https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk. You will have to install the SDK and search for vshadow.exe. Tho the SDK is for windows 10, it runs fine on Windows Server 2019, including the free Windows Server Hyper-V edition, which I love!
As for more detail as to what vshadow.exe is doing, check out https://docs.microsoft.com/en-us/windows/win32/vss/vshadow-tool-examples

Good Luck, 
