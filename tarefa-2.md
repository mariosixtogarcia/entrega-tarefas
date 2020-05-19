# Instalación MariaDB
## En máquina de Linux, con versión Ubuntu 18.04
### Esta maneira é moi simple para a comprensión, e lectura. Pero moi efectiva, que é o necesario.

#### Primeiro paso: Engadir o repositorio

Para engadir o repositorio necesitamos uns comandos que podemos encontrar na páxina oficial de MariaDB.

Eses comandos son os seguintes:

```
sudo apt-get install software-properties-common
sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
sudo add-apt-repository 'deb [arch=amd64] http://ftp.yz.yamagata-u.ac.jp/pub/dbms/mariadb/repo/10.4/ubuntu eoan main'
```

Ao utilizar ```sudo``` pediranos sempre a contrasinal de administrador, pero desta maneira aseguramonos instalar cos previlexios do usuario administrador.

Logo de ter o repositorio engadidido procedemos ao seguinte paso:

#### Segundo paso: Instalar MariaDB server.

Para iso levamos a cabo os seguintes comandos, dende a terminal.

```
sudo apt update
sudo apt install mariadb-server
```

Para verificar a instalación podemos usar o comando ```mysql -V``` , se nos di a versión de MariaDB que posuimos, é que a instalación foi correcta.

#### Último paso

Para finalizar xa, levamos a cabo o sefuinte comando, o cal nos configura o usuario root, e elemina certos usuarios innecesarios e que nos poden crear confusións.

```
sudo mysql_secure_installation
```

A todo o que nos pregunte contestaremos **y** que signica **yes**, e cando nos pida a contrasinal de root, proporcionaremoslla.


# Aqui observamos o xestor de mysql funcionando na miña máquina virtual

![funcionando](/Captura.PNG)
