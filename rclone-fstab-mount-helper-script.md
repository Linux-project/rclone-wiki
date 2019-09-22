## rclonefs helper script 

To enable mounting a volume using rclone via an entry in fstab the following helper script can be used:

    #!/bin/bash
    remote=$1
    mountpoint=$2
    shift 2

    # Process -o parameters
    while getopts :o: opts; do
        case $opts in
            o)
                params=${OPTARG//,/ }
                for param in $params; do
                    if [ "$param" == "rw"   ]; then continue; fi
                    if [ "$param" == "ro"   ]; then continue; fi
                    if [ "$param" == "dev"  ]; then continue; fi
                    if [ "$param" == "suid" ]; then continue; fi
                    if [ "$param" == "exec" ]; then continue; fi
                    if [ "$param" == "auto" ]; then continue; fi
                    if [ "$param" == "nodev" ]; then continue; fi
                    if [ "$param" == "nosuid" ]; then continue; fi
                    if [ "$param" == "noexec" ]; then continue; fi
                    if [ "$param" == "noauto" ]; then continue; fi
                    if [[ $param == x-systemd.* ]]; then continue; fi
                    trans="$trans --$param"
                done
                ;;
            \?)
                echo "Invalid option: -$OPTARG"
                ;;
        esac
    done

    # exec rclone
    trans="$trans $remote $mountpoint"
    # NOTE: do not try "mount --daemon" here, it does not play well with systemd automount, use '&'!
    PATH=$PATH rclone mount $trans </dev/null >/dev/null 2>/dev/null &

    # WARNING: this will loop forever if remote is actually empty!
    until [ "`ls -l $mountpoint`" != 'total 0' ]; do
        sleep 1
    done


Then in `/etc/fstab` you can add something like:

    rclonefs#remote:/path/to/remote/folder	/mnt/rclone	fuse	config=/home/user/.rclone.conf,allow-other,default-permissions,read-only,max-read-ahead=16M	0	0

Obviously, replace `/home/user/.rclone.conf` with the path to your config and replace `remote:/path/to/remote/folder` with the name of your remote the path you want to mount.

#### Important:
- rclonefs wrapper has to be in the PATH=/usr/local/bin:/usr/bin. mount is suid and doesn't ignores pre-set PATHs. Similarly, rclone invocation fails to find fusermount if not invoked with PATH=$PATH explicitly set.

## autofs

You can use the above mount wrapper with autofs, but note you **must** supply the `--allow-non-empty` option otherwise autofs will lock up entering the mount (see [#3246](https://github.com/ncw/rclone/issues/3246)).

An example config entry for autofs might look like this:

    remote	-fstype=fuse,config=/home/USER/.config/rclone/rclone.conf,allow-other,allow-non-empty	:rclonefs#remote:

## systemd

Alternatively if you're a `systemd` convert and want more control over when rclone mounts itself you can use a mount unit file. You will need to name this file after the [Where=](https://www.freedesktop.org/software/systemd/man/systemd.mount.html#Where=) directive, eg: `mnt-rclone.mount`

    [Unit]
    Description=rclone mount for remote:/path/to/remote/folder
    Requires=systemd-networkd.service
    Wants=network-online.target
    After=network-online.target

    [Mount]
    What=rclonefs#remote:/path/to/remote/folder
    Where=/mnt/rclone
    Type=fuse
    Options=auto,config=/home/user/.rclone.conf,allow-other,default-permissions,read-only,max-read-ahead=16M
    TimeoutSec=30

    [Install]
    WantedBy=multi-user.target
 