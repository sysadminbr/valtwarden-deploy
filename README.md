# Deploy do VaultWarden 
Deploy para 

VaultWarden - Deployment


Resumo


Justificativa



Pré-requisitos



=== Instalação do Docker
```
curl https://get.docker.com | sudo bash
```


=== Deploy do Vaulwarden container via docker-compose

criar a pasta onde estará os arquivos do projeto
```
mkdir -p docker_apps/vaultwarden && cd docker_apps/vaultwarden
```

Criar o arquivo docker-compose.yml com o seguinte conteúdo

Execute o comando abaixo para fazer o deploy e começar a rodar o Docker

```
sudo docker compose up -d
```


Configure o nginx com um certificado SSL (auto-assinado)

Gerar o certificado SSL
```
openssl req -new -x509 -newkey rsa:4096 -keyout vaultwarden.key -out vaultwarden.crt -nodes -subj "/CN=cofre.empresa.com.br" -days 3650 -addext "subjectAltName=DNS:cofre.empresa.com.br"
```

agora copie o certificado e chave gerados para a pasta comum
```
sudo cp vaultwarden.* /etc/ssl/private/
```

Instalar e Configurar o nginx
```
sudo apt install nginx -y
```



Configurar o nginx 

editar o arquivo /etc/nginx/sites-enabled/default com o seguinte conteúdo

upstream vaultwarden{
        server 127.0.0.1:8000;
}
server {
        listen 443 ssl;
        listen [::]:443 ssl ;
        ssl_certificate /etc/ssl/private/vaultwarden.crt;
        ssl_certificate_key /etc/ssl/private/vaultwarden.key;
        location / {
		proxy_set_header X-Real-IP       $remote_addr;
        	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://vaultwarden;
        }

}








== Impedindo o auto-registro de novos usuários

* Após o deploy inicial e registro da primeira conta adm, edite novamente o arquivo docker-compose.yml e altere o parâmetro SIGNUPS_ALLOWED=true para SIGNUPS_ALLOWED=false. 


=== Download dos clientes do cofre (browser plugin, desktop app, mobile, ...)

https://bitwarden.com/download/





