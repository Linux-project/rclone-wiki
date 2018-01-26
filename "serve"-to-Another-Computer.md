# rclone serve sharing
By default, `rclone serve http` and `rclone serve webdav` will open a server only accessible to `localhost` (the machine that `rclone` is running on).  This is intentional, as rclone does not have any built-in encryption or authentication for these modes. There are a couple of workarounds however.  With only rclone, you can overwrite the default, and have a non-authenticated, non-encrypted default shared over your network.  With another webserver ( such as [caddy](https://caddyserver.com)), you can create a proxy that allows some basic authentication and encryption functionalities.

***

# Option 1: Use only rclone.  No Encryption or Authentication:
**WARNING:**  This will make your remote accessible to everyone on your network, possibly the entire internet without a firewall.

**NOTE:** See Option 2 for a subjectively "better" solution.

## HTTP
Assuming your current command is: `rclone serve http remote:`

You can change it to: `rclone serve http --addr :8080 remote:`

Your remote will now be accessible from `http://yourip:8080`

## Webdav
Assuming your current command is:
`rclone serve webdav remote:`

You can change it to:
`rclone serve http --addr :8081 remote:`

Your remote will now be accessible on your ip on port 8081

***

# Option 2: Use an additional webserver on top of rclone ( Here we use [caddy](https://caddyserver.com) ):
**NOTE:** [Caddy](https://caddyserver.com) which is also written in go, and should be portable to all of the places rclone is.

**NOTE:** This Configuration will create a "self-signed" SSL certificate, which will cause clients to have to bypass warnings to connect.  If you have a signed SSL certificate, see [here](https://caddyserver.com/docs/tls) to see how you'll have to modify your `Caddyfile`.
1. Get Caddy: [caddyserver.com](https://caddyserver.com)
2. Create a file named `Caddyfile` in your current directory, and for contents, enter (replace `myaddress` with your ip or your FQDN ( Fully Qualified Domain Name. Assuming you have your router/firewall forwarding port 443 through to your ip, this will allow accessing your remote through the web), and `127.0.0.1:8080` with `127.0.0.1:8081` if you're using `rclone serve webdav`. **NOTE** Replace `$USER` and `$PASSWORD`!):
```
myaddress:443
proxy / 127.0.0.1:8080
basicauth / $USER $PASSWORD
tls self_signed
```
3. Run `rclone serve http myremote:` or `rclone serve webdav myremote:`.  This will create the server hosting your remote, but it will only be accessible from your own computer without encryption or authentication.
4. Run `caddy` from the directory where you put `Caddyfile`.  This will relay remote https or webdav requests, to rclone, requiring authentication, then encrypting them.  
5. You should now be able to connect over https or webdav, on `myaddress:443`


return [[Home]]