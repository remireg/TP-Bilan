# Installation de Zabbix et Wordpress avec Docker

---

## Etape 1 : Initialisation

#### Creation des dossiers qui vont accueillir Zabbix et Wordpress

```bash
mkdir Wordpress
mkdir Zabbix
```

## Etape 2 : Installation de Docker

#### Tele©chargement du script

- ```bash
  wget https://get.docker.com -O docker.sh
  ```
  

#### Installation du script

- ```bash
  ./docker.sh
  ```
  

> Docker et Docker-Compose sont en train de s'installer

## Etape 3 : Installation de Wordpress

#### Changement de chemin

- ```bash
  cd Wordpress
  ```
  

#### Creation du fichier docker-compose.yml

- ```bash
  nano docker-compose.yml
  ```
  

> Puis copier le contenu ci-dessous

- ```bash
  version: '3.1'
  
  services:
    wordpress:
      image: wordpress:latest
      restart: always
      ports:
        - 8080:80 # Ici vous pouvez modifier le port d'Ã©coute qui est 
                  # 8080 en ce que vous voulez, ici on aura 
                  # pour se connecter 192.168.x.x:8080
      environment:
        WORDPRESS_DB_HOST: db:3306   
        WORDPRESS_DB_USER: sio
        WORDPRESS_DB_PASSWORD: Sio2024  
        WORDPRESS_DB_NAME: db_sio
      volumes:
        - wordpress:/var/www/html
      depends_on:
        - db
      command: sh -c "sleep 10 && apache2-foreground"  
  
    db:
      image: mysql:8.0
      restart: always
      environment:
        MYSQL_DATABASE: db_sio           
        MYSQL_USER: sio
        MYSQL_PASSWORD: Sio2024           
        MYSQL_ROOT_PASSWORD: root_password  
      volumes:
        - db:/var/lib/mysql
  
  volumes:
    wordpress:
    db:
  ```
  

#### Installation de Wordpress

- ```bash
  docker compose up -d
  ```
  

## Etape 4 : Installation de Zabbix

#### Changement de chemin

- ```bash
  cd Zabbix
  ```
  

#### Creation du fichier docker-compose.yml

- ```bash
  nano docker-compose.yml
  ```
  

> Puis copier le contenu ci-dessous

- ```bash
  version: '3.7'
  
  services:
   
    mariadb:
      image: mariadb:latest
      container_name: mariadb-zabbix
      environment:
        MYSQL_ROOT_PASSWORD: root_password        
        MYSQL_DATABASE: zabbix                    
        MYSQL_USER: zabbix_user                   
        MYSQL_PASSWORD: zabbix_password            
      volumes:
        - mariadb-data:/var/lib/mysql
      networks:
        - zabbix-net
      restart: unless-stopped
  
  
    zabbix-server:
      image: zabbix/zabbix-server-mysql:latest
      container_name: zabbix-server-mariadb
      environment:
        DB_SERVER_HOST: mariadb-zabbix            
        MYSQL_USER: zabbix_user                  
        MYSQL_PASSWORD: zabbix_password           
        MYSQL_DBNAME: zabbix                      
      ports:
        - "10051:10051"
      depends_on:
        - mariadb
      networks:
        - zabbix-net
      restart: unless-stopped
  
  
    zabbix-frontend:
      image: zabbix/zabbix-web-nginx-mysql:latest
      container_name: zabbix-frontend
      environment:
        ZBX_SERVER_HOST: zabbix-server-mariadb      
        PHP_TZ: "UTC"
      ports:
        - "8081:80"
      depends_on:
        - zabbix-server
      networks:
        - zabbix-net
      restart: unless-stopped
  
  networks:
    zabbix-net:
      driver: bridge
  
  volumes:
    mariadb-data:
      driver: local
    zabbix-data:
      driver: local
  ```
  

---

## Zabbix et Wordpress sont installes
