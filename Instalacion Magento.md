# Instalación de Magento
Guía de cómo instalar, configurar y acceder al Magento CMS en una computadora que ejecuta Ubuntu Linux.
- Ubuntu 20.04.2, 2 CPU, 4GB RAM, 16GB HDD
- Magento Versión 2.4.0

Flujo de instalación
 
## 1. Prerrequisitos
Le recomendamos encarecidamente que actualice y actualice el software de su sistema operativo. Estas actualizaciones pueden proporcionar correcciones de seguridad y software que pueden evitar problemas futuros.

Ingrese los siguientes comandos como usuario con privilegios root:
```
sudo apt-get update
sudo apt-get upgrade
```

**Verificación de requisitos previos**

Para verificar los requisitos previos de su sistema, ingrese los siguientes comandos:

APACHE
```
apache2 -v
```

PHP
```
php -v
```

MySQL
```
mysql -V
```

Elasticsearch
```
curl -XGET 'localhost:9200
```

### 1.1 Instalación y configuración de Apache
**Versiones de Apache compatibles**

Magento es compatible con Apache 2.4.x.

**Instalación de Apache en Ubuntu**

Para instalar la versión predeterminada de Apache:

1.   Instalar Apache
```
apt-get -y install apache2
```

2. Verifique la instalación.
```
apache2 -v
```

El resultado se muestra similar al siguiente:
```
Server version: Apache/2.4.41 (Ubuntu)
Server built:   2020-08-12T19:46:17
```

3.   Habilite las reescrituras y *.htaccess* como se explica en las siguiente seccion.

**Habilitar reescrituras y .htaccess para Apache 2.4**

Utilice esta sección para habilitar la reescritura de Apache 2.4 y especificar una configuración para el archivo de configuración distribuido, _.htaccess_
Magento usa reescrituras de servidor y _.htaccess_ proporciona instrucciones a nivel de directorio para Apache.

1.	Habilite el módulo de reescritura de Apache:
```
a2enmod rewrite
```

2.	En Apache 2.4, el archivo de configuración del sitio predeterminado del servidor es _/etc/apache2/sites-available/000-default.conf_
Puede agregar lo siguiente al final de del archivo _000-default.conf_:
```
<Directory "/var/www/html">
    AllowOverride All
</Directory>
```
3. 	Reinicie Apache:
```
systemctl restart apache2.service
```

**Solución de errores 403 (prohibidos)**

Si encuentra errores 403 prohibidos al intentar acceder al sitio de Magento, puede actualizar su configuración de Apache o la configuración de su host virtual para permitir que los visitantes accedan al sitio como se explica:

Para permitir que los visitantes del sitio web accedan a su sitio, utilice una de las directivas Requeridas.
```
<Directory "/var/www/">
  Options Indexes FollowSymLinks MultiViews
  AllowOverride All
  Order allow,deny
  Require all granted
</Directory>
```

### 1.2 Instalación y configuración de MySQL
**Instalación de MySQL**

Magento 2.4 requiere una instalación limpia de MySQL 8.0. 
Para instalar MySQL, ejecute el siguiente comando:
```
sudo apt install mysql-server
```

Cuando finalice la instalación, se recomienda que ejecute un script de seguridad que viene preinstalado con MySQL. 
```
sudo mysql_secure_installation
```
Este script permite ajustar una serie de parámetros básicos de seguridad:

     *  Establecer una contraseña para la cuenta root.
     *  Permitir el acceso solo desde localhost para la cuenta root.
     *  Eliminar el acceso anónimo.
     *  Eliminar la base de datos de test, accesible por todos los usuarios, incluidos los anónimos, y eliminar los privilegios que permiten a cualquier usuario acceder a las bases de datos con nombres que empiezan por test_.

Una vez que se completa la instalación, el servidor MySQL debe iniciarse automáticamente. Puede verificar rápidamente su estado actual a través de systemd:
```
sudo systemctl status mysql
```

**Configurar la instancia de la base de datos de Magento**

Esta sección explica cómo crear una nueva instancia de base de datos para Magento.

1.	Acceda a un símbolo del sistema de MySQL.
```
sudo mysql -u root -p
```

2.	Ingrese la contraseña root del usuario de MySQL cuando se le solicite.
3.	Ingrese los siguientes comandos en el orden que se muestra para crear una instancia de base de datos nombrada _magento_ con nombre de usuario _magento_:
```
create database magento;
create user 'magento'@'localhost' IDENTIFIED BY 'Acceso2021!';
GRANT ALL ON magento.* TO 'magento'@'localhost';
flush privileges;
```

4.	Ingrese ```exit``` para salir del símbolo del sistema.

5.	Verifique la base de datos:
```
mysql -u magento -p
```

Si aparece el monitor MySQL, ha creado la base de datos correctamente. Si aparece un error, repita los comandos anteriores.

### 1.3 PHP

1.	Instale PHP 7.4 junto con los módulos necesarios
```
sudo apt install php7.4 libapache2-mod-php7.4 php-common php7.4-cli php7.4-common php7.4-json php7.4-opcache php7.4-readline php7.4-bcmath php7.4-curl php7.4-xml php7.4-gd php7.4-intl php7.4-mbstring php7.4-mysql php7.4-soap php7.4-xsl php7.4-zip
```

2.	Verificar extensiones instaladas
Magento requiere la instalación de un conjunto de extensiones:

-	ext-bcmath
-	ext-ctype
-	ext-curl
-	ext-dom
-	ext-gd
-	ext-hash
-	ext-iconv
-	ext-intl
-	ext-mbstring
-	ext-openssl
-	ext-pdo_mysql
-	ext-simplexml
-	ext-jabón
-	ext-xsl
-	ext-zip
-	ext-sockets

Para verificar las extensiones instaladas enumere los módulos instalados y verifique.
```
php -m
```

**Compruebe la configuración de PHP**

1.	busque los archivos de configuración de PHP
Encuentra el archivo _php.ini_ de configuración
Para encontrar la configuración del servidor web, ejecute un archivo _phpinfo.php_ en su navegador web y busque _Loaded Configuration File_.

**_phpinfo.php_** muestra una gran cantidad de información sobre PHP y sus extensiones.

Cree el archivo _php.info.php_ dentro del docroot del servidor web.
```
sudo vim /var/www/html/phpinfo.php
```

Agregue el siguiente código:
```
<?php
// Show all information, defaults to INFO_ALL
phpinfo();
```
Para ver los resultados, ingrese la siguiente URL en el campo de dirección o ubicación de su navegador:
```
http://<web server host or IP>/phpinfo.php
```
También se puede ubicar la configuración por la línea de comandos de PHP, ingrese:
```
php --ini | grep "Loaded Configuration File"
```
Mostrará la ruta de php.ini:
```
Loaded Configuration File:         /etc/php/7.4/cli/php.ini
```

2.	Encuentra opciones de configuración de OPcache
use el siguiente comando para localizarlo:
```
sudo find / -name 'opcache.ini'
```
Si tiene más de uno opcache.ini, modifíquelos todos.
```
/etc/php/7.4/mods-available/opcache.ini
/usr/share/php7.4-opcache/opcache/opcache.ini
```
**Cómo configurar las opciones de PHP**

Para configurar las opciones de PHP:
1.	Abra un php.ini en un editor de texto.
2.	Busque la zona horaria de su servidor. Busque la siguiente configuración y descomente si es necesario:

_date.timezone = America/Mexico_City_

3.	Cambie el valor de _memory_limit_ por uno los valores recomendados

_memory_limit=2G_

4.	Agregue o actualice la configuración realpath_cache para que coincida con los siguientes valores:
```
;
; Increase realpath cache size
;
realpath_cache_size = 10M
;
; Increase realpath cache ttl
;
realpath_cache_ttl = 7200
```

5.	Guarde sus cambios y salga del editor de texto.

**configurar las opciones de OPcache**

1.	Abra su archivo de configuración de OPcache en un editor de texto:
```
sudo vim /etc/php/7.4/mods-available/opcache.ini
sudo vim /usr/share/php7.4-opcache/opcache/opcache.ini
```

2.	Localice _opcache.save_comments_ y descomente si es necesario. Asegúrese de que su valor esté establecido en 1.
3.	Guarde sus cambios y salga del editor de texto.

Abra el archivo de configuración de Apache para establecer el nombre de servidor global
```
sudo vim /etc/apache2/apache2.conf
```

Agregue esta línea al final del archivo, luego guarde y salga:
```
ServerName localhost
```

Compruebe si hay errores de sintaxis que podamos haber introducido
```
sudo apache2ctl configtest
```

Reinicie su servidor web
```
service apache2 restart
```
### 1.4 Elasticsearch
**Instale el JDK en Ubuntu**

Para instalar JDK 1.8 en Ubuntu, ingrese los siguientes comandos como usuario con privilegios root:
```
apt-get -y update
apt-get install -y openjdk-8-jdk
```

Descargue e instale la clave de firma pública:
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

**Instalación desde el repositorio APT**

Es posible que deba instalar el paquete apt-transport-https antes de continuar:
```
sudo apt-get install apt-transport-https
```

Guarde la definición del repositorio en _/etc/apt/sources.list.d/elastic-7.x.list_:
```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
```

Puedes instalar el paquete de Elasticsearch con:
```
sudo apt-get update && sudo apt-get install elasticsearch
```

**SysV init vs system**

Elasticsearch no se inicia automáticamente después de la instalación. Cómo iniciar y detener Elasticsearch depende de si su sistema usa SysV inito systemd(usado por distribuciones más nuevas). Puede saber cuál se está utilizando, ejecutando este comando:
```
ps -p 1
```

Para configurar Elasticsearch para que se inicie automáticamente cuando se inicia el sistema, ejecute los siguientes comandos:
```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
```

Elasticsearch se puede iniciar y detener de la siguiente manera:
```
sudo systemctl start elasticsearch
sudo systemctl stop elasticsearch
```

Estos comandos no proporcionan información sobre si Elasticsearch se inició correctamente o no. En cambio, esta información se escribirá en los archivos de registro ubicados en _/var/log/elasticsearch/_

Para verificar que Elasticsearch está funcionando, ingrese los siguientes comandos:
```
curl -X GET 'http://localhost:9200'
```

Salida:
```
{
  "name" : "magento",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "Dq12DktKRQa0AArEuJ72Pw",
  "version" : {
    "number" : "7.12.1",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "3186837139b9c6b6d23c3200870651f10d3343b7",
    "build_date" : "2021-04-20T20:56:39.040728659Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
```
curl -XGET 'http://localhost:9200/_cat/health?v&pretty'
```

salida:
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1620873485 02:38:05  elasticsearch green           1         1      0   0    0    0        0             0                  -                100.0%
```

**Configuración de Elasticsearch**

Comencemos abriendo el archivo de configuración de Elasticsearch usando
```
sudo vim /etc/elasticsearch/elasticsearch.yml
```

Hay 3 cosas que queremos cambiar en este archivo mientras lo tenemos abierto:
1.	Actualización del nombre del clúster

Busque la línea que contiene cluster.name. Una vez que lo haya localizado, descomente y reemplace _my-application_ con algo descriptivo como _cluster-magento_.

2.	Actualizar el nombre del nodo

Ahora haga lo mismo con node.name, recordando eliminar el " #" anterior y luego reemplazar _nodo-1_ con algo descriptivo como _nodo-magento_.

3.	Actualización del host de red

Finalmente haz lo mismo con _network.host_, reemplazando la IP (“192.168.0.1”) por 10.0.0.25

Descomentar _http.port: 9200_

Ahora guarde sus cambios y salga del editor.
Recarga tus cambios
Para que esos 3 cambios surtan efecto, vuelva a cargar el servicio Elasticsearch ejecutando:
```
sudo systemctl restart elasticsearch
```

Ahora ejecutemos una verificación final para asegurarnos de que los cambios surtieron efecto y aplicaron los cambios:
```
curl -X GET 'http://localhost:9200'
```

salida:
```
{
  "name" : "nodo-magento",
  "cluster_name" : "cluster-magento",
  "cluster_uuid" : "Dq12DktKRQa0AArEuJ72Pw",
  "version" : {
    "number" : "7.12.1",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "3186837139b9c6b6d23c3200870651f10d3343b7",
    "build_date" : "2021-04-20T20:56:39.040728659Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

### 1.4 Establecer la propiedad y los permisos previos a la instalación
En esta sección se explica cómo establecer la propiedad y los permisos para su propio servidor o una configuración de alojamiento privado. En este tipo de configuración, normalmente no puede iniciar sesión o cambiar al usuario del servidor web. Por lo general, inicia sesión como un usuario y ejecuta el servidor web como un usuario diferente.

**Sobre el grupo compartido**

Para permitir que el servidor web escriba archivos y directorios en el sistema de archivos de Magento, pero también para mantener la propiedad del propietario del sistema de archivos de Magento, ambos usuarios deben estar en el mismo grupo. Esto es necesario para que ambos usuarios puedan compartir el acceso a los archivos de Magento (incluidos los archivos creados con el administrador de Magento u otras utilidades basadas en la web).

Esta sección explica cómo crear un nuevo propietario del sistema de archivos Magento y poner ese usuario en el grupo del servidor web. Puede utilizar una cuenta de usuario existente si lo desea; recomendamos que el usuario tenga una contraseña segura por razones de seguridad.

1.	Crea el propietario del sistema de archivos Magento y proporcione al usuario una contraseña segura. Para crear un usuario ingrese el siguiente comando como usuario con privilegios root:
```
sudo adduser magento
```

Para darle una contraseña al usuario, ingrese el siguiente comando como usuario con privilegios root:
```
sudo passwd magento
```
Siga las indicaciones en su pantalla para crear una contraseña para el usuario.

2.	Busque el grupo de usuarios del servidor web
Para encontrar el usuario de apache:
```
ps aux | grep apache
```

luego para buscar el grupo
```
groups <apache user>
```
Normalmente, el nombre de usuario y el nombre del grupo son ambos www-data.

3.	Coloque al propietario del sistema de archivos de Magento en el grupo del servidor web. Para agregar el usuario _magento_ al grupo _www-data_ principal:
```
usermod -a -G www-data magento
```

Para confirmar que su usuario de Magento es miembro del grupo de servidores web, ingrese el siguiente comando:
```
groups magento
```

El siguiente resultado de muestra muestra los grupos primario (magento) y secundario (www-data) del usuario .
```
magento: magento www-data
```

Para completar la tarea, reinicie el servidor web:
```
sudo systemctl restart apache2
```

4.	Permisos de carpeta

Cuando instalamos Apache, creó automáticamente un directorio web para almacenar archivos web (docroot). Sin embargo, habrá creado esto con el usuario predeterminado conocido como www-data(o incluso root). Entonces, necesitamos actualizar los permisos para ese directorio. Esto permitirá que nuestro nuevo usuario de Magento funcione correctamente.

Actualice la propiedad de la carpeta y el grupo para que coincida con nuestro nuevo usuario web:
```
sudo chown -R magento:www-data /var/www/html/
```

## 2. Obtén el metapaquete
Usamos Composer para administrar los componentes de Magento y sus dependencias
Descargar e instalar composer:
EL comando descargarán Composer de su página de mantenimiento y lo instalarán en el directorio _/usr/local/bin_ , este es un directorio global local para ejecutables de aplicaciones.
```
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```
**Para obtener el metapaquete de Magento:**
 1.	Inicie sesión en su servidor Magento o cambie al propietario del sistema de archivos Magento.
```
su magento
```
 2.	Cambie al directorio docroot del servidor web o un directorio que haya configurado como un docroot de host virtual.
```
cd /var/www/html/
```
 3.	Cree un nuevo proyecto de Composer utilizando el metapaquete de Magento Open Source o Magento Commerce.
```
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition magento
```
Durante la configuración, se le pedirá un nombre de usuario y una contraseña. Para que quede claro, Nombre de usuario = Clave pública y Contraseña = Clave privada obtenidas de la cuenta de Magento Marketplace
```
Public Key: 44e09e45efe15d4c90b70c0370886dbd
Private Key: cd9275d55beb9895558edc091a0178c8
```
**Establecer permisos de archivo**

Debe establecer permisos de lectura y escritura para el grupo de servidores web antes de instalar el software Magento. Esto es necesario para que la línea de comandos pueda escribir archivos en el sistema de archivos de Magento.

```
cd /var/www/html/magento

find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +

find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +

chown -R :www-data . 

chmod u+x bin/magento
```

Para ejecutar comandos de Magento desde cualquier directorio, agregue _<magento_root>/bin_ a su sistema PATH.
```
export PATH=$PATH:/var/www/html/magento/bin
```
**Instale Magento desde la línea de comandos .**

En este ejemplo se supone que el directorio de instalación de Magento es nombrado magento, el db-host está en la misma máquina (localhost), y que el db-name, db-usery son magento: 
```
./magento setup:install \
--base-url=http://magento.ticss.ddns.net/magento \
--db-host=localhost \
--db-name=magento \
--db-user=magento \
--db-password=Acceso2021! \
--admin-firstname=admin \
--admin-lastname=admin \
--admin-email=admin@admin.com \
--admin-user=admin \
--admin-password=admin123 \
--language=en_US \
--currency=USD \
--timezone=America/Mexico_City \
--use-rewrites=1
```

## 3. Verificar la instalación
Vaya al escaparate en un navegador web. Ingrésela en la dirección o barra de ubicación de su navegador.
```
http://magento.ticss.ddns.net/magento
```

