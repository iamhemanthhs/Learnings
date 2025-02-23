
## 1. Download Domain SSL
You can download an SSL certificate using the following JSON:

```json
{
   "apikey": "your-api-key",
   "domain": "example.com",
   "action": "download",
   "file": "cert" // file can be "CSR", "cert", "CA", "privatekey"
}
```

### CURL Command:
```sh
curl -H "Content-Type: application/json" --data '{"apikey": "your-api-key", "domain": "hemanthhs.work.gd", "action": "download", "file": "cert"}' https://api.dnsexit.com/dns/lse.jsp
```

## 2. Generate DNS API Key
To use the SSL API for managing SSL remotely, you need an API key:

1. Login to your account.
2. Click on "Settings" on the left menu.
3. Click "DNS API Key" to access the API Key creation page.
4. Use the generated key for every remote SSL request.

## 3. Configure SSL on EC2 Server

### Save Certificate Files
```sh
sudo nano /etc/nginx/ssl/cert
sudo nano /etc/nginx/ssl/privatekey
sudo nano /etc/nginx/ssl/ca
```

### Configure Nginx
```sh
sudo nano /etc/nginx/nginx.conf
```

#### Example Nginx Configuration (`/etc/nginx/nginx.conf`):
```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:AES128-GCM-SHA256:HIGH:!aNULL:!MD5';
    ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    gzip on;
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### Configure SSL for Your Domain
```sh
sudo nano /etc/nginx/sites-available/default
```

#### Example Site Configuration (`/etc/nginx/sites-available/default`):
```nginx
server {
    listen 443 ssl;
    server_name hemanthhs.work.gd;  # Replace with your domain

    ssl_certificate /etc/nginx/ssl/cert;
    ssl_certificate_key /etc/nginx/ssl/privatekey;
    ssl_trusted_certificate /etc/nginx/ssl/ca;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:AES128-GCM-SHA256:AES256-GCM-SHA384';

    root /var/www/html;
    index index.html index.htm;

    location / {
        proxy_pass http://127.0.0.1:8000/;  # Gunicorn is running on port 8000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Restart Nginx & Configure Firewall
```sh
sudo systemctl restart nginx
sudo ufw status
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

