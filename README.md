# Deploy do VaultWarden via docker compose

# Resumo  
O vaultwarden é uma implementação alternativa e leve do servidor bitwarden, compatível com a API original e escrito na linguagem Rust, perfeito para hospedar por conta própria e também é compatível com os plugins e clientes oficiais do Bitwarden.

# Justificativa  
Criamos este deploy para já trazer diversas opções pré-definidas que você provavelmente irá usar em produção. A documentação oficial do Vaultwarde traz exemplos simples e uma documentação extensa. Este repositório serve como um facilitador do deploy. Para casos de uso mais avançados, indicamos que use a documentação oficial do Vaultwarden como guia.  


# Instalação  

## Instale o docker
```
curl https://get.docker.com | sudo bash
```

## Clonar este repositório
```
git clone https://github.com/sysadminbr/vaultwarden-deploy
```

## Acessar a pasta do projeto
```
cd vaultwarden-deploy
```

## Editar e ajustar os parâmetros do Vaultwarden
```

```


## Realizar o deploy
```
sudo docker compose up -d
```


## (Opcional) Gerar um certificado auto-assinado para o nginx servir HTTPS
```
# altere cofre.empresa.com.br para um domínio seu
openssl req -new -x509 -newkey rsa:4096 -keyout vaultwarden.key -out vaultwarden.crt -nodes -subj "/CN=cofre.empresa.com.br" -days 3650 -addext "subjectAltName=DNS:cofre.empresa.com.br"

# agora copie o certificado e chave gerados para a pasta comum
sudo cp vaultwarden.* /etc/ssl/private/
```

## Instale o nginx
```
sudo apt install nginx -y
```



## Configurar o nginx 
### Edite o arquivo /etc/nginx/sites-enabled/default com o seguinte conteúdo
```
# onde buscar o upstream (porta do container)
upstream vaultwarden{
        server 127.0.0.1:8000;
}
# definição do endpoint http, redirecionando para https
server {
        listen 80;
        listen [::]:80;
        location / {
		proxy_set_header X-Real-IP       $remote_addr;
        	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://vaultwarden;
        }
}
# definição do endpoint https
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
```








== Impedindo o auto-registro de novos usuários

* Após o deploy inicial e registro da primeira conta adm, edite novamente o arquivo docker-compose.yml e altere o parâmetro SIGNUPS_ALLOWED=true para SIGNUPS_ALLOWED=false. 


=== Download dos clientes do cofre (browser plugin, desktop app, mobile, ...)

https://bitwarden.com/download/





