# practica04

# Creación de una segunda máquina virtual para Apache.

# A tener en cuenta:

Esta práctica es igual que la anterior que hemos hecho, la diferencia mas notoria es que emplearemos tres maquinas:

- Dos de ellas actuaran de front-ent que las dos contendran el servidor web apache HTTP.
- La tercera, back-end,donde instalaremos el servicio de MySQL.

Cada una de las maquina tendran su propia Ip por lo que la estructura se quedara con las dos maquinas de ApacheHTTP en paralelo y la máquina de MySQL conectada a las dos.

Lo que queremos conseguir con esta practica sera crear una arquitectura con alta disponibilidad que sea escalable y redundante de modo que alternara la carga entre los dos servidores web.

## VagrantFile

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  # Apache HTTP Server
  config.vm.define "web1" do |app|
    app.vm.hostname = "web1"
    app.vm.network "private_network", ip: "192.168.33.10"
    app.vm.provision "shell", path: "apache.sh"
  end

  # Apache HTTP Server
  config.vm.define "web2" do |app|
    app.vm.hostname = "web2"
    app.vm.network "private_network", ip: "192.168.33.11"
    app.vm.provision "shell", path: "apache.sh"
  end

  # MySQL Server
  config.vm.define "db" do |app|
    app.vm.hostname = "db"
    app.vm.network "private_network", ip: "192.168.33.12"
    app.vm.provision "shell", path: "mysql.sh"
  end

end
```

## Script

### Scrip Apache
```
#!/bin/bash

# instalacion de apache
apt-get update
apt-get install -y apache2
apt-get install -y php libapache2-mod-php php-mysql
sudo /etc/init.d/apache2 restart


#clonar repositorios
apt-get install -y git
cd /tmp
rm -rf iaw-practica-lamp 
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git

#copiar repositorio

cd iaw-practica-lamp
cp src/* /var/www/html/

#modificar la base de datos que queremos usar

sed -i 's/localhost/192.168.33.12/' /var/www/html/config.php
chown www-data:www-data /var/www/html/* -R

#borramos el index

rm -rf /var/www/html/index.html
```

### Script MySQL 

```
#!/bin/bash
apt-get update
apt-get -y install debconf-utils

DB_ROOT_PASSWD=root
debconf-set-selections <<< "mysql-server mysql-server/root_password password $DB_ROOT_PASSWD"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $DB_ROOT_PASSWD"

apt-get install -y mysql-server
sed -i -e 's/127.0.0.1/0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf
#/etc/init.d/mysql restart

#mysql -uroot mysql -p$DB_ROOT_PASSWD <<< "GRANT ALL PRIVILEGES ON *.* TO root@'%' IDENTIFIED BY '$DB_ROOT_PASSWD'; FLUSH PRIVILEGES;"

#clonar repositorios
apt-get install -y git
cd /tmp
rm -rf iaw-practica-lamp 
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git

#crear base de datos
mysql -u root -p$DB_ROOT_PASSWD < /tmp/iaw-practica-lamp/db/database.sql

/etc/init.d/mysql restart
```
