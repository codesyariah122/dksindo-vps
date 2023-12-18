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
