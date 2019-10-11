# Install Teleport
### find the file on https://gravitational.com/teleport/download/
>`yum install wget`  
>`wget https://get.gravitational.com/teleport-v4.1.0-linux-amd64-bin.tar.gz`  
>`tar -xzf teleport-v4.1.0-linux-amd64-bin.tar.gz`  
>`cd teleport`  
>`sudo ./install`  

### check correct installation
>`teleport start` 

### install daemon
>`cd /teleport/examples/systemd`  
>`sudo cp teleport.service /etc/systemd/system/teleport.service`  
>`sudo systemctl daemon-reload`  
>`sudo systemctl enable teleport`  
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
https://gravitational.com/teleport/docs/admin-guide/#configuration-file
