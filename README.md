# proxy-setup

## To get Internet connection in command line
Following instruction is valid for *Windows* with GitBash and for *Linux*.

Insert into `~/.bashrc` file the following functions:

```bash
# Set Proxy
function setproxy() {
        export {http,https,ftp}_proxy="http://username:password@company_proxy_url:port"
        export {HTTP,HTTPS,FTP}_PROXY="http://username:password@company_proxy_url:port"
}

# Unset Proxy
function unsetproxy() {
        unset {http,https,ftp}_proxy
        unset {HTTP,HTTPS,FTP}_PROXY
}
```

Then in command line run `source ~/.bashrc` or restart the bash session to get the functions available.
To set proxy settings just run `setproxy` and verify it by running `env` in bash.

### Proxy for apt package maneger (Ubuntu), but probably transferable to other distros
It might be that the proxy is set, but `apt` package manager does not work.

Insert in the follwing file `/etc/apt/apt.conf` 
```
Acquire::http::proxy "http://username:password@company_proxy_url:port";
Acquire::https::proxy "https://username:password@company_proxy_url:port";
Acquire::ftp::proxy "ftp://username:password@company_proxy_url:port";
```
There are probably other ways, but this one works.

### pip, etc
It should be sufficient to run `setproxy` function described above.

## Working with Github (or other git service) via SSH

### In Windows 
By installing [Git for Windows](https://gitforwindows.org/) `connect.exe` is included in the installation 
In `~/.ssh/config` insert for GitHub configuration 
`ProxyCommand /mingw64/bin/connect.exe -H proxy_company_url:port %h %p`.

Example:
```bash
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/dev_key
  ProxyCommand /mingw64/bin/connect.exe -H proxy_company_url:port %h %p
  TCPKeepAlive yes
  IdentitiesOnly yes
```

Test if GitHub SSH connection works
```
ssh -T git@github.com
```

### In Linux
Install [corkscrew](https://github.com/bryanpkc/corkscrew)

Change the ProxyCommand line in `~/.ssh/config` file. Here's an example:
```
ProxyCommand /usr/local/bin/corkscrew proxy_work_url port %h %p ~/.ssh/myauth
```
where `~/.ssh/myauth` contains proxy username and password
```
username:password
```

Full example of `config`:
```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/dev_key
  TCPKeepAlive yes
  IdentitiesOnly yes
  ProxyCommand /usr/local/bin/corkscrew proxy_work_url 8080 %h %p ~/.ssh/myauth
```

Test if GitHub SSH connection works
```
ssh -T git@github.com
```

## To get Internet connection in WSL
Most likely the problem is with DNS resolution, which can be fixed by inserting into `/etc/resolv.conf` (located in WSL machine)
```
nameserver ip_address
```
`ip_address` can be identified in Windows via GitBash via `nslookup` command.

Example:
```
$ nslookup google.com
Server:  nsc.dlh.de
Address:  57.20.120.97

Nicht autorisierende Antwort:
Name:    google.com
Addresses:  2a00:1450:4001:81c::200e
          142.250.185.238
```
Here `ip_address` is 57.20.120.97.
