# Install Teleport
### find the file on https://gravitational.com/teleport/download/
>`yum install wget && \`  
>`wget https://get.gravitational.com/teleport-v4.1.1-linux-amd64-bin.tar.gz && \`  
>`tar -xzf teleport-v4.1.1-linux-amd64-bin.tar.gz && \`  
>`cd teleport && \`  
>`sudo ./install`  

### check correct installation
>`teleport start` 

### install daemon
>`cd /teleport/examples/systemd && \`  
>`sudo cp teleport.service /etc/systemd/system/teleport.service && \`  
>`sudo systemctl daemon-reload && \`  
>`sudo systemctl enable teleport && \`  
>`sudo systemctl start teleport`  

### To check on Teleport daemon status:
>`systemctl status teleport`  

### To take a look at Teleport system log:
>`journalctl -fu teleport`  


### add new user
>`sudo tctl users add $USER`  
Signup token has been created. Share this URL with the user:
https://localhost:3080/web/newuser/96c85ed60b47ad345525f03e1524ac95d78d94ffd2d0fb3c683ff9d6221747c2

## Install a node

### on master
>`sudo tctl nodes add`  

### on node
>`teleport start \`  
>`   --roles=node \  `  
>`   --token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \`  
>`   --ca-pin=sha256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \`  
>`   --auth-server=XX.XX.XX.XX:3025`  `  

### Edit config file:

>`sudo vi /etc/teleport.yaml`

##### master config sample:
```yaml
teleport:
  auth_service:
    enabled: yes
    cluster_name: "<clustername>"
    listen_addr: <ipaddress>:3025
    public_addr: <url>:3025

  ssh_service:
    enabled: yes
    listen_addr: <ipaddress>:3025
    public_addr: <url>:3025
    labels:
      role: master
```

##### node config sample:
```yaml
teleport:
  auth_token: 7df6f8fce95ebdcbe9cdda10852891b5
  ca_pin: "sha256:50edc00d1bcbf94623d85b49fe2a28b1faceca436d21f88a0d387cd5db15b2b9"
  auth_servers:
      - 192.168.1.80:3025
auth_service:
  enabled: no
ssh_service:
    enabled: yes
    labels:
      role: client
proxy_service:
  enabled: no
```


https://gravitational.com/teleport/docs/admin-guide/#configuration-file

