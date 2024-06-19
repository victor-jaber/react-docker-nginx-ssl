# Iniciar React com Proxy Reverso do NGINX 🚀

## Passos para Configurar o Proxy Reverso para sua Aplicação React

Certifique-se de seguir os passos abaixo para configurar sua aplicação React com um proxy reverso do NGINX. 

### Pré-requisitos 📋

- Docker Swarm configurado
- NGINX configurado para gerenciar SSL
- Pasta `react-app` disponível no repositório
- Serviço `sf-api` já configurado

### Passo 1: Build da Aplicação React ⚙️

1. Baixe a pasta `react-app`.
2. Navegue até a pasta `react-app`.
3. Rode o comando:

    ```bash
    npm run build
    ```

### Passo 2: Configuração do NGINX 🛠️

Edite o arquivo `app.conf` com o seguinte conteúdo:

```nginx
server {
    server_name nginx.devsipremo.com;

    root /usr/share/nginx/html; # Caminho do build do React
    index index.html index.htm;

    location / {
        try_files $uri $uri/ /index.html;
        proxy_pass http://react-app:80;  # Direciona para o serviço react-app
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Configurações de cache (exemplo)
    location ~* \.(css|js|jpg|jpeg|png)$ {
        expires 6h; # Exemplos: max, 30m, 1y, off
        log_not_found off;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/nginx.devsipremo.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/nginx.devsipremo.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = nginx.devsipremo.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name nginx.devsipremo.com;

    # Redireciona todas as requisições HTTP para HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}
```
### Passo 3: Iniciar os Serviços 🖥️

Após criar o build e alterar o `app.conf`, volte para a pasta principal e execute:

```
sh sh-up.sh
```
### Resultado Final 🎉

Se tudo estiver configurado corretamente, sua aplicação React estará rodando no domínio configurado (`nginx.devsipremo.com`) e estará utilizando SSL.
