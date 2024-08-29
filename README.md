# Instalação do MariaDB, Nginx, PHP e Composer
Adicionar o comando (add-apt-repository)
```sh
sudo apt -y install software-properties-common curl apt-transport-https ca-certificates gnupg
```
Adicionar repositórios adicionais para PHP e MariaDB
```sh
sudo LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
```
⚠️ O comando abaixo não é necessário se você estiver usando o Ubuntu 22 ou superior
```sh
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
```
Atualizar a lista de repositórios
```sh
sudo apt update
```
Instalar dependências
```sh
sudo apt -y install php8.3 php8.3-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip,intl} mariadb-server nginx tar unzip
```
Instalando o Composer
```sh
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

# Exemplo de configuração do Nginx com PHP
Substitua **MEUSITE** pelo nome do seu projeto
```sh
sudo nano /etc/nginx/sites-enabled/MEUSITE.conf
```
Exemplo de um arquivo de configurações, lembre-se de trocar **MINHAPASTA** para redirecionar para o diretório correto
```
server {
    listen 1000;
    server_name localhost;
    root /var/www/html/MINHAPASTA;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    client_max_body_size 100m;
    client_body_timeout 120s;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param PHP_VALUE "upload_max_filesize = 100M \n post_max_size=100M";
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_PROXY "";
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
# Criando um usuário root no MariaDB
Fazendo login no MariaDB
```sh
sudo mariadb -u root
```
Criando um novo usuário, lembre-se de editar **usuario** e **senha**
```sql
CREATE USER 'usuario'@'127.0.0.1' IDENTIFIED BY 'senha';
```
Liberando todas permissões para um usuário específico, lembre-se de editar **usuario**
```sql
GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'127.0.0.1' WITH GRANT OPTION;
```
Sair do console do MariaDB
```sql
EXIT;
```
