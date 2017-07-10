# Third Party Integrations with rclone

This page documents projects which use rclone for some purpose.  If you have a project using rclone then please feel free to add a section on it here.  This should include a URL and a paragraph of description.

## Hashbackup ##

http://www.hashbackup.com

HashBackup is an efficient multi-threaded command-line backup system for Linux, FreeBSD, and OSX. Features: multiple versions of files, file retention, combining small files into larger arc files, splitting huge files, variable-block dedup, local encryption, mountable backups (fuse), backup verification, and support for many "dumb" storage systems such as S3, Google, ftp, rsync, ssh, etc. Rclone is supported as a transport protocol for storage systems not natively supported by HashBackup.

## rhttpserve ##

https://github.com/brandur/rhttpserve

A tiny HTTP server that can serve files out of any rclone remote. Includes a command line utility to generate time expiring Ed25519-based signed URLs (similar to a signed S3 URL) that will be verified by the server before it agrees to send a file.

This one is sort of "meware" in that it's largely created to be personally useful, but could potentially be interesting to someone else.

## RcloneBrowser ##

A simple cross-platform GUI for rclone: https://mmozeiko.github.io/RcloneBrowser/
Works on Windows, macOS and GNU/Linux.
