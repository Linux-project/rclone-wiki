I have wrote a script to install rclone on windows using PowerShell. I have wrote this for my own use but am sharing it. The default settings need Admin rights, as it installs the latest beta into the system32 folder. If you have feature requests I have started a thread on the forums located here

https://forum.rclone.org/t/install-script-for-rclone-on-windows/3149

To use the script you can simply run this line in PowerShell

    Invoke-Expression ((Invoke-WebRequest -Uri "https://gist.githubusercontent.com/justusiv/1ff2ad273cea3e33ca4acc5cab24c8e0/raw").content)
    install-rclone

As with any code please review it before running it on your system.
