# Third Party Integrations with rclone

This page documents projects which use rclone for some purpose.  If you have a project using rclone then please feel free to add a section on it here.  This should include a URL and a paragraph of description.

## HashBackup ##

http://www.hashbackup.com

HashBackup is an efficient multi-threaded command-line backup system for Linux, FreeBSD, and OSX. 

Features: scales to huge backups, multiple versions of files, file retention, combining small files into larger arc files, splitting huge files, variable-block dedup, local encryption, mountable backups (fuse), backup verification, and support for many storage systems: local disks, Amazon S3, Google Storage, Backblaze B2, Rackspace Cloud Files, WebDAV, ftp, rsync, ssh, and imap/email. Backups are sent to one or more destinations as the backup occurs and new destinations are automatically populated with earlier backup data.  Rclone can be used as a transport protocol for storage systems not natively supported by HashBackup.

## rhttpserve ##

https://github.com/brandur/rhttpserve

A tiny HTTP server that can serve files out of any rclone remote. Includes a command line utility to generate time expiring Ed25519-based signed URLs (similar to a signed S3 URL) that will be verified by the server before it agrees to send a file.

This one is sort of "meware" in that it's largely created to be personally useful, but could potentially be interesting to someone else.

## RcloneBrowser ##

A simple cross-platform GUI for rclone: https://mmozeiko.github.io/RcloneBrowser/
Works on Windows, macOS and GNU/Linux.

## rclone_jobber ##

[rclone_jobber.sh](https://github.com/wolfv6/rclone_jobber) is a Bash script that calls rclone sync to perform a backup job.
An rclone_jobber tutorial includes backup-job and restore-job examples for a home computer.

Features:
move old files to dated_directory (easy to restore deleted directory),
move old files to dated_files (easy to brows file history, like Time Machine),
abort if job is already running (maybe previous run didn't finish),
pop-up for error conditions,
logging,
free.

## Unified Cloud Storage ##

An Android App to manage data on different cloud storage providers. It uses rclone in the background to connect to different cloud drives.
https://play.google.com/store/apps/details?id=ch.ethz.idsc.unifiedcloudstorage

## Doomsday Machine ## 

[Doomsday Machine](https://github.com/johnjones4/Doomsday-Machine-2) is a tool for backing up many cloud services to a local machine including IMAP email, Evernote, Google Contacts, Todoist, GitHub projects, LastPass data, and others. It uses RClone as one of the many tools that perform a nightly backup of services to a Docker container and then archives the backup for long term storage.

## PlexInTheCloud ##

[PlexInTheCloud](https://github.com/chrisanthropic/PlexInTheCloud) is a series of bash scripts to install & configure: Plex, nzbget, sickrage, couchpotato, mylar, with rclone mounted Google Drive storage and full post-processing on your VPS (virtual private server). Includes a wiki with clear documentation

## RCloneSync ##
[RCloneSync.py](https://github.com/cjnaz/RCloneSync) provides bi-directional sync capability utilizing rclone.  This tool was developed and debugged on Dropbox and Google Drive on Python 2.7 on Centos 7.  I run it periodically as a cron job to sync the cloud services with a local drive which is Samba-served on my LAN.  The official Dropbox and Drive services generally do not play well with network share drives.

## RcloneOSX ##
[RcloneOSX](https://github.com/rsyncOSX/rcloneosx) is a macOS GUI utilizing rclone. It is compiled with support for macOS 10.11 - 10.13. RcloneOSX executes rclone tasks as single tasks, as batch tasks and by schedule.

## rclone4pi  - Easy Install onto a Raspberry Pi
[rclone4pi](https://github.com/pageauc/rclone4pi) includes an automated bash install script that can be run from a [curl command](https://github.com/pageauc/rclone4pi/wiki#quick-install) or [manually](https://github.com/pageauc/rclone4pi/wiki#manual-install). [Wiki](https://github.com/pageauc/rclone4pi/wiki) instructions are provided that includes setting up a [Cloud Storage Service](https://github.com/pageauc/rclone4pi/wiki#how-to-configure-a-remote-storage-service). A demo [***rclone-sync.sh***](https://github.com/pageauc/rclone4pi/blob/master/rclone-sync.sh) script is provided. It can be run manually or added as a [crontab](https://github.com/pageauc/rclone4pi/wiki#how-to-automate-rclone) and prevents multiple instances of rclone from running. This avoids running multiple instances of the same sync job.

## UpBack ##
[UpBack](https://github.com/DavideRossi/upback) is a two way synchronization utility based on rclone. It assumes a star topology for your backup, that means a remote storage that is synchronized with multiple clients (your workstation, laptop, HTPC, ...).

## rclone QPKG for QNAP ##
[RClone QPKG for QNAP](https://qnapclub.eu/fr/qpkg/330) provides a QPKG to install rclone easily on your QNAP NAS.