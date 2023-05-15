### 1. Install Nginx

```
sudo yum install nginx
```
Start Nginx service
```
sudo systemctl enable nginx && sudo systemctl start nginx
```
Now, let's assume that we want to configure NGINX to act as a reverse proxy on port 80 for a service running on port 5555.

Let's open the NGINX configuration file:
```
sudo vim /etc/nginx/nginx.conf
```
And let's modify the server configuration by changing the value of YOUR_DOMAIN with the domain name associated with the EC2 instance (if any), 
and EC2_PRIVATE_IPV4_ADDRESS with the private IPv4 address of the EC2 instance, which can be retrieved from the AWS control panel:
```
 server {
        listen       80;
        listen       [::]:80;
        server_name  <YOUR_DOMAIN>;
        location / {
            proxy_pass http://<EC2_PRIVATE_IPV4_ADDRESS>:5555;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }

```
After saving and closing the file, let's run a test to verify if the configuration is valid and restart the process:
```
sudo nginx -t
```
Restart nginx service
```
sudo systemctl restart nginx
```
Now, if the configuration is correct, it should be possible to reach our service through the address http://<EC2_ELASTIC_IP> or http://<YOUR_DOMAIN>.
### Install Cerbot
For installing Certbot and enabling HTTPS on NGINX, we will rely on Python. So, first of all, let's set up a virtual environment:
```
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
```
Afterwards, run this command to install Certbot:
```
sudo /opt/certbot/bin/pip install certbot certbot-nginx
```
Now, execute the following command to ensure that the certbot command can be run:
```
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```
Finally, run the following command to obtain a certificate and let Certbot automatically modify the NGINX configuration, enabling HTTPS:
```
sudo certbot --nginx
```
After following the certificate generation wizard, we will be able to access our EC2 instance via HTTPS using the address https://<EC2_ELASTIC_IP> or https://<YOUR_DOMAIN>.
### Set up automatic renewal
To enable Certbot to automatically renew the certificates, it is sufficient to add a cron job by running the following command:
```
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```
