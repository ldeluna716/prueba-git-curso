# Instalación de Magento
Guía de cómo instalar, configurar y acceder al Magento CMS en una computadora que ejecuta Ubuntu Linux.
-Ubuntu 20.04.2, 2 CPU, 4GB RAM, 16GB HDD
-Magento Versión 2.4.0

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
```apache2 -v```

PHP
```php -v```

MySQL
```mysql -V```

Elasticsearch
```curl -XGET 'localhost:9200```

### 1.1 Instalación y configuración de Apache
**Versiones de Apache compatibles**
Magento es compatible con Apache 2.4.x.

**Instalación de Apache en Ubuntu**
Para instalar la versión predeterminada de Apache:
1.	Instalar Apache
```apt-get -y install apache2```
2.	Verifique la instalación.
```apache2 -v```

`	El resultado se muestra similar al siguiente:
```
Server version: Apache/2.4.41 (Ubuntu)
Server built:   2020-08-12T19:46:17
```
3.	Habilite las reescrituras y *.htaccess* como se explica en las siguiente seccion.

**Habilitar reescrituras y .htaccess para Apache 2.4**
Utilice esta sección para habilitar la reescritura de Apache 2.4 y especificar una configuración para el archivo de configuración distribuido, _.htaccess_
Magento usa reescrituras de servidor y _.htaccess_ proporciona instrucciones a nivel de directorio para Apache.

1.	Habilite el módulo de reescritura de Apache:
```a2enmod rewrite```

2.	En Apache 2.4, el archivo de configuración del sitio predeterminado del servidor es _/etc/apache2/sites-available/000-default.conf_
Puede agregar lo siguiente al final de del archivo _000-default.conf_:
```
<Directory "/var/www/html">
    AllowOverride All
</Directory>
	```
3.	Reinicie Apache:
```systemctl restart apache2.service```

**Solución de errores 403 (prohibidos)**
Si encuentra errores 403 prohibidos al intentar acceder al sitio de Magento, puede actualizar su configuración de Apache o la configuración de su host virtual para permitir que los visitantes accedan al sitio como se explica:

Resolviendo 403 errores prohibidos para Apache 2.4
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
```sudo apt install mysql-server```

Cuando finalice la instalación, se recomienda que ejecute un script de seguridad que viene preinstalado con MySQL. 
```sudo mysql_secure_installation```
Permite ajustar una serie de parámetros básicos de seguridad:
     *  Establecer una contraseña para la cuenta root.
     *  Permitir el acceso solo desde localhost para la cuenta root.
     *  Eliminar el acceso anónimo.
     *  Eliminar la base de datos de test, accesible por todos los usuarios, incluidos los anónimos, y        eliminar los privilegios que permiten a cualquier usuario acceder a las bases de datos con nombres que empiezan por test_.
Una vez que se completa la instalación, el servidor MySQL debe iniciarse automáticamente. Puede verificar rápidamente su estado actual a través de systemd:
```sudo systemctl status mysql```

**Configurar la instancia de la base de datos de Magento**
Esta sección explica cómo crear una nueva instancia de base de datos para Magento.

1.	Acceda a un símbolo del sistema de MySQL.
sudo mysql -u root -p

2.	Ingrese la contraseña root del usuario de MySQL cuando se le solicite.
3.	Ingrese los siguientes comandos en el orden que se muestra para crear una instancia de base de datos nombrada magento con nombre de usuario magento:
create database magento;
create user 'magento'@'localhost' IDENTIFIED BY 'Acceso2021!';
GRANT ALL ON magento.* TO 'magento'@'localhost';
flush privileges;

4.	Ingrese exit para salir del símbolo del sistema.

5.	Verifique la base de datos:

mysql -u magento -p

Si aparece el monitor MySQL, ha creado la base de datos correctamente. Si aparece un error, repita los comandos anteriores.

