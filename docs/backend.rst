Backend (cosechador LAReferencia)
=================================

Requerimientos
~~~~~~~~~~~~~~
* Acceso al repositorio de código https://github.com/lareferencia/lrharvester
* Acceso al repositorio de código https://github.com/lareferencia/lareferencia-docs
* Git
* Java JDK 8
* PostgreSQL
* Apache Tomcat 8.0.X
* Apache Maven

Instalación en Ubuntu 18.04.2 LTS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
La ejecución de las siguientes instrucciones debe realizarse identificado como usuario “root”.

Creación del usuario “lareferencia” para el sistema operativo
-------------------------------------------------------------

The following is a SQL statement.

.. code-block:: console

   adduser lareferencia

El sistema operativo solicitará la contraseña, su confirmación y algunos datos:

.. code-block:: console

   Enter new UNIX password: 
   Retype new UNIX password: 
   passwd: password updated successfully
   Enter the new value, or press ENTER for the default
   Full Name []: LA Referencia
   Room Number []:
   Work Phone []:
   Home Phone []:
   Other []:
   Is the information correct? [Y/n] y


Instalación de Git
------------------
El código del software LAReferencia se encuentra en un repositorio versionado Git.  Por ello para poder acceder al código fuente se debe instalar Git en el sistema operativo.

.. code-block:: console

   sudo apt-get update
   sudo apt-get install git

Para corroborar la correcta instalación puede ejecutarse

.. code-block:: console

   git --version

Instalación del JDK 8
---------------------
Dado que durante la instalación del software LAReferencia es requerida la compilación del código fuente, se requiere instalar el JDK.

.. code-block:: console

   sudo apt-get install openjdk-8-jdk

Para corroborar la correcta instalación puede ejecutarse

.. code-block:: console

   java -version

Se procede a establecer la variable de entorno “JAVA_HOME”.

.. code-block:: console

   sudo sh -c 'echo export JAVA_HOME=\"/usr/lib/jvm/java-8-openjdk-amd64\" > /home/lareferencia/.bash_profile'

Instalación de PostgreSQL
-------------------------
El software LAReferencia utiliza PostgreSQL como base de datos relacional para almacenar la configuración del “backend”, los datos de los repositorios cosechados y los resultados de las cosechas.

.. code-block:: console

   sudo apt install postgresql 

Una vez instalado el gestor de bases de datos, se procede a la creación del usuario “lrharvester”

.. code-block:: console

   su postgres
   createuser --pwprompt --interactive lrharvester
   Enter password for new role: *****
   Enter it again: *****
   Shall the new role be a superuser? (y/n) n
   Shall the new role be allowed to create databases? (y/n) y
   Shall the new role be allowed to create more new roles? (y/n) n

Siempre logueado como postgres, se crea la base de datos “lrharvester” con el usuario “lrharvester” como dueño

.. code-block:: console

   createdb -O lrharvester lrharvester

Instalación de Apache Maven
---------------------------
El software LAReferencia requiere de la herramienta Apache Maven para automatizar las tareas de compilación y construcción de las aplicaciones java.  Mediante Maven se descargan e instalan las dependencias necesarias para la correcta compilación de las aplicaciones java.

.. code-block:: console

   apt-get install maven

Para corroborar la correcta instalación puede ejecutarse

.. code-block:: console

   mvn -version 

Instalación de Apache Tomcat 8
------------------------------
Las aplicaciones java del software LAReferencia, requieren de un contenedor de servlets para poder desplegarse en un servidor web.  Por ello debe instalarse Tomcat 8.

.. code-block:: console

   apt-get install tomcat8

Dado que uno de los servicios del software LAReferencia debe utilizar el puerto 8080, es necesario cambiar el puerto de conexión por defecto de Tomcat (8080) por el 8090.  Esto se realiza en el archivo /etc/tomcat8/server.xml

.. code-block:: xml

   <Connector port=”8090” protocol=”HTTP/1.1”
        connectionTimeout=”20000”
        URIEncoding=”UTF-8”
        redirectPort=”8443” />

Es necesario configurar el uso de la memoria para Apache Tomcat.  Esto se realiza en el archivo /etc/default/tomcat8

.. code-block:: console

   JAVA_OPTS =”-Djava.awt.headless=true -Xmx2048m -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode”

Descarga del código fuente de LAReferencia
------------------------------------------
Ubicarse en la carpeta /home/lareferencia y ejecutar lo siguiente:

.. code-block:: console

   git clone https://github.com/lareferencia/lrharvester.git

Instalación del backend
-----------------------
El backend es el módulo del software LAReferencia específico para la cosecha, validación y transformación de metadatos.

En el archivo /home/lareferencia/lrharvester/backend/pom.xml se cambia de manera temporal el packaging de “jar” por “war”

.. code-block:: xml

   <artifactId>backend</artifactId>
   <version>3.3</version>
   <packaging>war</packaging>
   <name>LAReferencia</name>
   <url></url>

Seguidamente en la carpeta /home/lareferencia/lrharvester/backend/etc.lrharvester, se copia el archivo “backend.properties.model” como “backend.properties”.

.. code-block:: console

   cp backend.properties.model backend.properties

En el archivo backend.properties se editan los datos para la conexión a la base de datos creada previamente

.. code-block:: console

#db config
db.engine=postgres
db.host=localhost
db.name=lrharvester
db.user=lrharvester
db.passwd=*****
db.port=5432

Se genera un enlace simbólico en /etc apuntando a la carpeta /home/lareferencia/lrharvester/backend/etc.lrharvester

.. code-block:: console

   ln -s /home/lareferencia/lrharvester/backend/etc.lrharvester/ /etc/lrharvester

Se procede a compilar el código con Apache Maven.  Para ello en la carpeta /home/lareferencia/lrharvester/backend se ejecuta

.. code-block:: console

   mvn clean package

Finalmente, el .war generado tras la compilación exitosa debe copiarse en el directorio webapps de tomcat.

.. code-block:: console

   cp target/backend-3.3.war /var/lib/tomcat8/webapps/backend.war

Para corroborar la correcta instalación del backend, se inicia el tomcat.

.. code-block:: console

   /etc/init.d/tomcat8 start

Y se ingresa en el explorador http://localhost:8090/backend.

Importación del validador y las reglas de validación
----------------------------------------------------
Obtener desde el repositorio de documentación los archivos .sql correspondientes

.. code-block:: console

   git clone https://github.com/lareferencia/lareferencia-docs.git

En la carpeta “Tablas para el backend” se encuentran los archivos: “validator.sql”, “validatorrule.sql”, “transformer.sql” y “transformerrule.sql”.  Los primeros 2 corresponden a las tablas del validador y sus reglas.  Los últimos 2 corresponden a las tablas del transformador y sus reglas.

Importación del transformador y las reglas de transformación
------------------------------------------------------------

Para importar el transformador ejecutar lo siguiente, siempre identificado como usuario postgres:

.. code-block:: console

   psql lrharvester < validator.sql
   psql lrharvester < validatorrule.sql
