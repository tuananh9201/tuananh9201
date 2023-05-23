# Deploy simple fastapi to ubuntu server
### 1. Add service file
```
sudo vim /etc/systemd/system/fastapi.service
```
### 2. Update service file
```
[Unit]
Description=Simple Fastapi web server

[Service]
Restart=always
WorkingDirectory=/some/path
ExecStart=/usr/bin/bash -c 'source /home/ibuild/anaconda3/bin/activate;cd /home/ibuild/backend;./prestart.sh

[Install]
WantedBy=default.target

```
### 3. enable service
```
sudo sytemctl enable fastapi.service
```
### 4. start service
```
sudo systemctl start fastapi
```
