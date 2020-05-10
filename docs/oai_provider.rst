OAI Provider (proveedor de datos)
=================================

Requerimientos
~~~~~~~~~~~~~~
* Acceso al repositorio de código https://github.com/lareferencia/lrharvester
* Git
* Java JDK 8
* Apache Tomcat 8
* Apache Maven

Instalación Ubuntu 18.04.2 LTS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
NOTA: La ejecución de las siguientes instrucciones debe realizarse identificado como usuario “root”.

Compilación del código del proveedor de datos
---------------------------------------------
Ubicado en el directorio /lareferencia/lrharvester/xoai-provider ejecutar
.. code-block:: console

  mvn clean package

Se detiene el Tomcat
.. code-block:: console

  /etc/init.d/tomcat8 stop

Se copia y renombra el archivo generado tras la compilación del directorio target a /var/lib/tomcat8/webapps/
.. code-block:: console

  cp target/xoai-2.0.war  /var/lib/tomcat8/webapps/oai.war

Para corroborar la correcta instalación del backend, se inicia el tomcat.
.. code-block:: console

  /etc/init.d/tomcat8 start

Y se ingresa en el explorador http://localhost:8090/oai.
