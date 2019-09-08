**The goal of this wiki to show you how easy it is to enable VSS for rclone or any windows app.**
***
As per [Wikipedia](https://en.wikipedia.org/wiki/Shadow_Copy):
> Volume Snapshot Service or VSS is a technology included in Microsoft Windows that can create backup copies or snapshots of computer files or volumes, even when they are in use. A snapshot is a read-only point-in-time copy of the volume. Snapshots allow the creation of consistent backups of a volume, ensuring that the contents do not change and are not locked while the backup is being made.

I use rclone to copy very large files created by Veeam Backup and Replication which can take 24+ hours, depending on the size of the files and internet speeds. 

If Veeam backup software would run again while rclone is still uploading, Veeam would modify the set of files that rclone is still uploading, resulting in a corrupted and uselss set of backup files. Or if I wanted rclone to check a set of large sized files, Veeam might run again and modify the local files while rclone is still checking and generate errors in the log files.

So I create a VSS read-only point-in-time snapshot and have rclone use that as the source.
Now rclone can takes its time, upload, sync, check or whatever and not be concerned that the data will be modified.
1. The snapshot data is read-only. Attempting to delete a file will generate a read-only error.
2. The snapshot data is never in-use. rclone will not get an error about in-use files.
3. The snapshot data is never locked. rclone will not get an error about locked files.

Yeah, you want that, you know you need that, and you can have it with a few lines of code.  

Let's say that I want to sync c:\data\ to the cloud.
1 - Create a file named vs.cmd:

    vshadow.exe -nw -script=setvar-vshadow.cmd -exec=exec.cmd c:

2 - Create a file named exec.cmd file:

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\
    rclone sync c:\snapshot\data\ dest:data
    rmdir c:\snapshot\ /q

Execute vs.cmd and that is all it takes to create a shadow mount.
When vs.cmd is run, it will execute vshadow.exe.

vshadow.exe will:
1. Create a file named setvar-vshadow.cmd will some variables that needs to be passed to exec.cmd.
2. Create the snapshot.
3. Execute exec.cmd.

exec.cmd will:
1. Call setvar-vshadow.cmd, to load the variiables created by vshadow.exe.
2. Create a symbolc link to the snapshot.
3. Run rclone with the source as c:\snapshot\data\, not c:\data\.
4. Delete the symbolc link.
Note that c:\snapshot\ is a temporaty link only accessible while exec.cmd is running. when exec.cmd exits, the snapshot is removed by Windows operating system.

I find it confusing, that c:\snapshot\ is a mirror image of c:\; when writing more complex scripts, this confusion was leading to errors.
So I will give you 3 workarounds for clear code for exec.cmd.

--- Use SUBST command.

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\ 
    subst t: c:\
    rclone sync t:\snapshot\data\ dest:data
    subst t: /d
    rmdir c:\snapshot\ /q

--- Use NET USE command

    call setvar-vshadow.cmd
    mklink /d c:\snapshot\ %shadow_device_1%\ 
    net share snapshot=c:\
    rclone sync \\localhost\snapshot\data\ dest:data
    net share snapshot /delete
    rmdir c:\snapshot\ /q

--- Create a new drive letter. This is what I do as this is the most reliable and least code needed.
Create a new drive and use that for the mount point.
Most Windows computer hard drives will not have free space to create a new drive. So shrink your c: drive by just 1GB and use that space to create new partition and name it the b: drive. Since the b: drive will only be used to mount the vss point, 1GB is plenty of space, in fact 1MB is enough space.

    call setvar-vshadow.cmd
    mklink /d b:\snapshot\ %shadow_device_1%\
    rclone sync b:\snapshot\data\ dest:data
    rmdir b:\snapshot\ /q

Good luck and if you have any questions or comments, please do not hesitate to contact me.
Create a post at the forum and put the "VSS question" in the subject and I will answer.

Now for the boring details.
Vshadow.exe is part of the Window SDK which can be download free from Micro$oft at https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk. You will have to install the SDK and search for vshadow.exe. Tho the SDK is for windows 10, it runs fine on Windows Server 2019, including the free Windows Server Hyper-V edition, which I love!

As for more detail as to what vshadow.exe is doing, check out https://docs.microsoft.com/en-us/windows/win32/vss/vshadow-tool-examples
