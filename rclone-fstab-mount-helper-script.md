To enable mounting a volume using rclone via an fstab entry, a following script can be used:

    #!/bin/bash
    
    src=$1
    dst=$2
    
    shift 2
    
    # Process -o parameters
    while getopts :o: opts; do
    	case $opts in
    		o)
    			parms=`echo $OPTARG | sed -e 's/,/ /g'`
    			for parm in $parms; do
    				if [ $parm == "rw"   ]; then continue; fi
    				if [ $parm == "dev"  ]; then continue; fi
    				if [ $parm == "suid" ]; then continue; fi
    				if [ $parm == "exec" ]; then continue; fi
    				if [ $parm == "noexec" ]; then continue; fi
    				if [ $parm == "nosuid" ]; then continue; fi
    				if [ $parm == "nodev" ]; then continue; fi
    				trans="$trans --$parm"
    			done
    			;;
    		\?)
    			echo "Invalid option: -$OPTARG"
    			;;
    	esac
    done
    
    trans="$trans $src $dst"
    
    PATH=$PATH rclone mount $trans &
    sleep 5


Then in fstab you can add something like:

    rclonefs#crypt:/path /mnt/tmp fuse config=/home/user/.rclone.conf,allow-other,default-permissions,read-only,max-read-ahead=16M 0 0

Obviously, change the path to your config appropriately, and the name of the remote (in the above case "crypt" and the paths.

Important:
rclonefs wrapper has to be in the PATH=/usr/local/bin:/usr/bin. mount is suid and doesn't ignores pre-set PATHs. Similarly, rclone invocation fails to find fusermount if not invoked with PATH=$PATH explicitly set.
