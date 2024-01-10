# dksindo-vps

### Database vhost

```
server {
  server_name database.xxx.xxx;

  root /var/www/html/phpmyadmin;
  index index.php index.html index.htm;

  location / {
        try_files $uri $uri/ /index.php;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock; # Sesuaikan versi PHP yang Anda instal
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        allow all;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/****.***/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/****.***/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = database.****.***) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


  listen 80;
  server_name database.****.***;
    return 404; # managed by Certbot

}
```

### Api Service System Background

**on /etc/systemd/system/sirmuh-api.server.service**

```
[Service]
ExecStart=/var/www/html/sirmuh-pos-api-backend/start.sh
WorkingDirectory=/var/www/html/sirmuh-pos-api-backend/
Restart=always
User=www-data
Group=www-data

[Install]
WantedBy=multi-user.target

```

#### Api service vhost :

**on /etc/nginx/sites-available/sirmuh-api**

```
server {
  server_name sirmuh.api.****.***;

  location / {
    proxy_pass http://103.175.221.221:9001;
  }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/****.***/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/****.***/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = sirmuh.***.****.***) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


  listen 80;
  server_name sirmuh.***.****.***;
    return 404; # managed by Certbot
}
```

### NuxtJS as Ui System Background

**on /etc/systemd/system/sirmuh.ui.service**

```
[Unit]
Description=Sirmuh UI Service
After=network.target

[Service]
ExecStart=/var/www/html/sirmuh-pos-ui-frontend/start.sh
WorkingDirectory=/var/www/html/sirmuh-pos-ui-frontend
Restart=always

[Install]
WantedBy=multi-user.target

```

#### Nuxtjs AS ui vhost :

**on /etc/nginx/sites-available**

```
server {
  server_name sirmuh.****.***;

  location / {
    proxy_pass http://localhost:3001;
    proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
  }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/****.***/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/****.***/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = sirmuh.****.***) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


  listen 80;
  server_name sirmuh.****.***;
    return 404; # managed by Certbot
}

```

#### SSL Library Doc :

[!ssl_certbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)

### Domain Host :

`CloudFlare`

### Command

**Running daemon**

```
systemctl daemon-reload
systemctl restart app.service
systemctl status app.service
```

#### Using portainer docker

##### portainer installation

```
apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable

apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io
usermod -aG docker $(whoami)

apt update && apt install nginx -y
```

##### Docker process after installation

```
docker pull portainer/portainer-ce
docker volume create portainer_data
docker run --name portainer -h portainer -d -p 8000:8000 -p 127.0.0.1:9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

##### Upgrade Portainer

```
docker stop portainer
docker rm portainer
docker pull portainer/portainer-ce
docker run --name portainer -h portainer -d -p 8000:8000 -p 127.0.0.1:9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce

docker start portainer
```

**_Access portainer web interface:_**

```
http://your-ip:9000
```

#### Run Application with pm2 inside portainer

##### Vhost for container application in host server

**_create vhost file:_**

```
nano /etc/nginx/sites-available/your-application-vhost
```

**_sites-available/your-application-vhost:_**

```
server {
  server_name your-app.com;

  location / {
    proxy_pass http://your-ip-public:9091;
    proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
  }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/dksindo.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/dksindo.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = your-app.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


  listen 80;
  server_name your-app.com;
    return 404; # managed by Certbot
}
```

**_Create symbolic links:_**

```
ln -s /etc/nginx/sites-available/your-application-vhost /etc/nginx/sites-enabled
```

##### Install pm2

```
yarn install pm2 -D

```

**setup nginx default vhost / your app vhost :**

```

vi /etc/nginx/sites-available/default

```

**_my default nginx vhost :_**

```

server {
listen 80 default_server;

location / {
proxy_pass http://localhost:9091;
}
}

```

**_inside nodejs project directory:_**
file start.sh :

```

#!/bin/bash
yarn start

```

**Run With pm2 command**

```

pm2 start yarn --name "sirmuh-pos-ui" -- start

```

**_Check pm2 status list :_**

```

pm2 list

id │ name │ mode │ ↺ │ status │ cpu │ memory │
├────┼────────────────────┼──────────┼──────┼───────────┼──────────┼──────────┤
│ 0 │ sirmuh-pos-ui │ fork │ 0 │ online │ 0% │ 80.4mb │

```
