## Create user and their home directory
```
sudo mkdir -p /var/sftphome/sftpuser
sudo groupadd sftp
sudo useradd -m sftpuser -g sftp -d /var/sftphome/sftpuser
sudo passwd sftpuser
sudo chown root:sftp /var/sftphome/sftpuser     # make sure it's owned by root!!
# run the next two commands if an upload directory is required
sudo mkdir /var/sftphome/sftpuser/upload
sudo chown sftpuser:sftp /var/sftphome/sftpuser/upload
```

## Change /etc/ssh/sshd_config with settings as follow:
```
Subsystem sftp	/usr/lib/openssh/sftp-server
Match User sftpuser
ChrootDirectory %h
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
```