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

## Unified Cloud Storage ##

An Android App to manage data on different cloud storage providers. It uses rclone in the background to connect to different cloud drives.
https://play.google.com/store/apps/details?id=ch.ethz.idsc.unifiedcloudstorage

## Doomsday Machine ## 

[Doomsday Machine](https://github.com/johnjones4/Doomsday-Machine-2) is a tool for backing up many cloud services to a local machine including IMAP email, Evernote, Google Contacts, Todoist, GitHub projects, LastPass data, and others. It uses RClone as one of the many tools that perform a nightly backup of services to a Docker container and then archives the backup for long term storage.
