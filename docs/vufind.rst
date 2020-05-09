Vufind (portal e indexador público)
===================================

Requerimientos
Vufind 4.1.3 https://github.com/vufind-org/vufind/releases/download/v4.1.3/vufind-4.1.3.tar.gz
PHP 7
Instrucciones para Ubuntu 18.04.2 LTS
*La ejecución de las siguientes instrucciones debe realizarse identificado como usuario “root”.

Instalación de PHP

sudo apt-get install php

*Instalar mediante apt, instaló la versión 7.2.

Instalación de los módulos php requeridos por vufind

sudo apt-get install php-mbstring php-pear php-dev php-gd php-intl php-json php-ldap php-xml php-soap php-curl php-pgsql php-mysql

El servidor Apache debe reiniciarse

systemctl restart apache2

Creación de usuario y base de datos en PostgreSQL

su - postgres
createuser --pwprompt --interactive vufind
Enter password for new role: *****
Enter it again: *****
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) y
Shall the new role be allowed to create more new roles? (y/n) n
El usuario vufind que recién se creó será dueño de la base de datos con el mismo nombre

createdb -O vufind vufind

Instalación del “indexador”
Para iniciar, se descarga el código de vufind4.

wget https://github.com/vufind-org/vufind/releases/download/v4.1.3/vufind-4.1.3.tar.gz

Seguidamente se extrae el contenido del archivo comprimido

tar -vzxf vufind-4.1.3.tar.gz

Y se renombra la carpeta con el contenido extraído

mv vufind-4.1.3 vufind

Se configuran las siguientes variables de entorno: VUFIND_HOME, VUFIND_LOCAL_DIR y SOLR_HOME

sudo sh -c 'echo export VUFIND_HOME=\"/home/lareferencia/vufind\"  >> /home/lareferencia/.bash_profile'
 
sudo sh -c 'echo export VUFIND_LOCAL_DIR=\"/home/lareferencia/vufind/local\"  >> /home/lareferencia/.bash_profile'
 
sudo sh -c 'echo export SOLR_HOME=\"/home/lareferencia/vufind/solr/vufind\"  >> /home/lareferencia/.bash_profile'

Y se cargan al entorno las variables recientemente configuradas

source /home/lareferencia/.bash_profile

En la carpeta /home/lareferencia/vufind/solr/vufind, se deshabilitan los cores: “authority”, “reserves” y “website”.  Para ello, dentro de las respectivas carpetas, se renombra el archivo core.properties a core.properties.off

cd /home/lareferencia/vufind/solr/vufind/authority
mv core.properties core.properties.off
 
cd /home/lareferencia/vufind/solr/vufind/reserves
mv core.properties core.properties.off
 
cd /home/lareferencia/vufind/solr/vufind/website
mv core.properties core.properties.off

Luego se copian desde la carpeta /home/lareferencia/lrharvester/backend/solr.cores los propios de LAReferencia.  Estos son “oai” y “vstats”.

cp -R oai/ /home/lareferencia/vufind/solr/vufind 
cp -R vstats/ /home/lareferencia/vufind/solr/vufind

Se crea un enlace simbólico en /usr/local que apunta a VUFIND_HOME

ln -s /home/lareferencia/vufind /usr/local/vufind

En la carpeta /home/lareferencia/vufind se ejecuta 

php install.php

Se iniciará un diálogo interactivo.  Todas las preguntas se contestan con el valor por defecto.

VuFind has been found in /home/lareferencia/vufind.
Where would you like to store your local settings? [/home/lareferencia/vufind/local]
 
VuFind supports use of a custom module for storing local code changes.
If you do not plan to customize the code, you can skip this step.
If you decide to use a custom module, the name you choose will be used for the module's directory name and its PHP namespace.
 
What module name would you like to use? [blank for none]
What base path should be used in VuFind's URL? [/vufind]
Se le cambia el dueño a las siguientes carpetas

chown -R lareferencia /home/lareferencia/vufind
chown -R www-data:www-data /usr/local/vufind/local/cache
chown -R www-data:www-data /usr/local/vufind/local/config
chown www-data:www-data /usr/local/vufind/local/config/vufind

Se habilita la línea de comandos para vufind

mkdir /usr/local/vufind/local/cache/cli
chmod 777 /usr/local/vufind/local/cache/cli

Configuración de la conexión a la base de datos en vufind
En el archivo /usr/local/vufind/local/config/vufind/config.ini se actualizan los datos necesarios para la conexión con la base de datos creada en el paso anterior. 

database = pgsql://vufind:*****@localhost/vufind

NOTA: Los asteriscos corresponden a la contraseña del usuario vufind.

Se enlaza el archivo de configuración de vufind con el servidor Apache

ln -s /usr/local/vufind/local/httpd-vufind.conf /etc/apache2/conf-enabled/vufind.conf

Se habilita el “mod rewrite” para el servidor Apache y se reinicia el servicio.

a2enmod rewrite
systemctl restart apache2

Se pone en ejecución el solr con el usuario “lareferencia”

/usr/local/vufind/solr.sh start

Para corroborar la correcta instalación de vufind, ingresar en el explorador http://localhost/vufind.

Wizard de instalación en http://localhost/vufind/install


