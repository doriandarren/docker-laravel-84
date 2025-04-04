# Docker Settings

## Crear Proyectos

```sh
composer create-project --prefer-dist laravel/laravel proyecto1
```



```sh
docker compose build --no-cache             # Construir la imagen
docker compose up -d                        # Levantar el servidor
docker exec -it laravel_server bash         # Entrar al servidor


## APACHE
docker logs laravel_server                  ### ver logs
docker logs laravel_server --follow         ### ver logs on live

## NGINX
docker logs nginx-proxy                     ### ver logs
docker logs nginx-proxy --follow            ### ver logs on live


a2ensite portuarios.globalfleet.test.conf
service apache2 reload

```



## Reiniciar el Apache

```sh
service apache2 restart
```





## Para problemas:

```sh

# 1️⃣ Eliminar todos los contenedores y volúmenes
docker-compose down --volumes

# 2️⃣ Eliminar caché y volúmenes antiguos
docker system prune -af
docker volume rm $(docker volume ls -q)

# 3️⃣ Volver a construir la imagen sin caché
docker-compose build --no-cache

# 4️⃣ Iniciar los contenedores
docker-compose up -d

# 5️⃣ Verificar que el contenedor está corriendo
docker ps
```



## Agregar Proyecto:

### 1.- Nuevo archivo en docker/nginx/api.splytin.test.conf

```sh
server {
    listen 80;
    server_name api.splytin.test;

    location / {
        proxy_pass http://laravel_server:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```



### 2.- Nuevo archivo en docker/apache/sites/api.splytin.test.conf


```sh
<VirtualHost *:80>
    ServerName api.splytin.test
    DocumentRoot /var/www/projects/api.splytin.com/public

    <Directory /var/www/projects/api.splytin.com/public>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/apache2/api_splytin_error.log
    CustomLog /var/log/apache2/api_splytin_access.log combined
</VirtualHost>
```


### 3.- En la MacOs:


```sh

sudo nano /etc/hosts

127.0.0.1 api.splytin.test


// Limpia la caché DNS
sudo dscacheutil -flushcache

```



### 4.- Reiniciar los contenedores

```sh
docker compose down
docker compose up -d --force-recreate
```
