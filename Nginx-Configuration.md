### Proxy Configuration

Replace the variables between `{{` and `}}` with their corresponding values if they are used:
- `nginx_host`: Host/Domain name for your DGLux Server that nginx will respond to.
- `nginx_port`: Port you want nginx to listen on.
- `dglux_port`: Port that DGLux Server is running on.
- `ssl_certificate_path`: Path to the SSL certificate.
- `ssl_certificate_key_path`: Path to the key for the SSL certificate.

```nginx
map $http_upgrade $connection_upgrade {
  default upgrade;
  ''   '';
}

upstream dglux {
  server localhost:{{dglux_port}} max_fails=0 fail_timeout=30s;
  keepalive 32;
}

server {
    server_name {{nginx_host}};

    #### Include if you do not want SSL. ####
    listen {{nginx_port}};
    #########################################

    #### Include if you want SSL. ####
    listen {{nginx_port}} ssl;
    ssl_certificate {{path_to_certificate}};
    ssl_certificate_key {{path_to_certificate_key}};
    ##################################

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