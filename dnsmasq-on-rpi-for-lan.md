## Steps
```
sudo apt install -y dnsmasq
sudo cat << EOF > /etc/dnsmasq.conf
no-resolv
domain=raspberry.local    
min-port=4096   
server=8.8.8.8
server=8.8.4.4
EOF

sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved

systemctl start dnsmasq
systemctl enable dnsmasq
```

## confirm
```bash
dig www.google.com @localhost
```