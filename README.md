## TUTORIAL ANSIBLE 

### Adicionando Conteúdo ao website

Crie uma pasta `static-site-src`

``` 
mkdir static-site-src
cd static-site-src
```

Dentro dessa pasta crie um arquivo `index.html` com o seguinte conteúdo

```
<h1>Welcome to Ansible</h1>
<img src="/ansible-logo.jpg" />
```

Agora baixe um arquivo de imagem dentro dessa pasta

```
curl -sL https://www.ansible.com/hubfs/Images/Red-Hat-Ansible_OG_1200x630.png -o ansible-logo.png
```

## Criando os arquivos de configuração

Voltando um nivel anterior crie os arquivos de configuração para o nginx

* Arquivo `nginx.conf`

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include /etc/nginx/conf.d/*.conf;

}
``` 

* Crie o arquivo default.conf

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /usr/share/nginx/html;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
}
```

## Criando o playbook

Crie o arquivo site.yml

```
---
- hosts: all
  tasks:
    - name: install dependences
      yum: name=epel-release state=present

    - name: ensure nginx is at the latest version
      yum: name=nginx state=latest

    - name: copy the nginx config
      copy:
        src: ./nginx.conf
        dest: /etc/nginx/nginx.conf

    - name: copy the default config file
      copy:
        src: ./default.conf
        dest: /etc/nginx/conf.d/default.conf

    - name: copy the content of the web site
      copy:
        src: ./static-site-src/
        dest: /usr/share/nginx/html

    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

Rode o playbook

```
ansible-playbook -i inventory site.yml
``` 
