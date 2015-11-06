## For HTTP Proxies
```
upstream dglux {
  server localhost:{{server_port}} max_fails=0 fail_timeout=30s;
  keepalive 32;
}

server {
    listen 80;

    ssl_certificate {{path_to_certificate}};
    ssl_certificate_key {{path_to_certificate_key}};
    server_name {{server_host}};

    location / {
        proxy_pass http://dglux;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $http_host;
    }

    location /ws {
        proxy_pass http://dglux;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $http_host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
```