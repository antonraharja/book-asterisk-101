# Remote Access

OpenSSH server installation in Ubuntu:

```
apt-get install openssh-client
apt-get install openssh-server
```

OpenSSH client and server installation in CentOS:

```
yum install openssh-client
yum install openssh-server
```

On Microsoft Windows:

* Use [WinSCP](http://winscp.net/eng/index.php) as SCP client.
* Use [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) as SSH client

## SSH Secure Shell

Usage:

```
ssh <username>@<remote host>
ssh -p <remote SSH port> <username>@<remote host>
```

Example:

```
ssh root@192.168.0.141
ssh support@myasteriskpbx.vpn
ssh -p 2000 root@192.168.1.137
```

## SCP Remote copy

Usage:

```
scp <local file> <username>@<remote host>:<remote directory>
scp -P <remote SSH port> <local file> <username>@<remote host>:<remote directory>
scp <username>@<remote host>:<remote path to file> <local directory or file>
```

Example:

```
scp /var/log/asterisk/full root@192.168.0.141:~/logs
scp -P 2000 /etc/asterisk/sip.conf support@192.168.0.141:/etc/asterisk/sip.conf
scp support@myasteriskpbx.vpn:/etc/asterisk/extensions.conf ~/backup/
```

## Additional information

Please note that you cannot remote copy a folder using SCP, it must be files.

For example, to copy the whole content of ```/etc/asterisk``` you can use these command:

```
scp /etc/asterisk/* root@mybackupserver.vpn:/home/backup/asterisk/etc/
scp -r /etc/asterisk root@mybackupserver.vpn:/home/backup/asterisk/etc/
```

Note on the ```-r``` option added to ```scp``` command, it means recursively copying all files and directories under certain directory.

Or better, compress them first and then SCP.

Compressing files then SCP it, example:

```
tar -zcvf /root/backup-etc-asterisk.tar.gz /etc/asterisk/
scp /root/backup-etc-asterisk.tar.gz root@mybackupserver.vpn:/home/backup/asterisk/etc/
```

# Passwordless Login

Should you need to auto-login for example for scripted auto-backup using `scp`, you need to know how to configure passwordless login or to be exact login using SSL certificate.

From our terminal (laptop or PC) you need to generate key (or use existing if you already have it):

```
ssh-keygen
```

Copy our public key to the server. In this example our public key is in `~/.ssh/id_rsa.pub` and our user on remote server with IP `192.168.1.12` is `support`:

```
ssh-copy-id -i ~/.ssh/id_rsa.pub support@192.168.1.12
```

Testing login without typing password:

```
ssh support@192.168.1.12
```

Example of copying file to the server:

```
scp ~/Desktop/edited-conf/sip.conf support@192.168.1.12:/etc/asterisk/sip.conf
```
