Networkutils (utilitario para modificar redes por lotes)
========================================================

En el directorio /home/lareferencia/lrharvester/networkutils/src/main/resources abrir el archivo application.properties.

En este archivo deben configurarse los datos para la conexión con la base de datos

.. code-block:: console

  spring.datasource.url=jdbc:postgresql://localhost:5432/nombreDeLaBase
  spring.datasource.username=usuario
  spring.datasource.password=contraseña

Luego, en el directorio /home/lareferencia/lrharvester/networkutils debe ejecutarse:
.. code-block:: console

  mvn clean package install -DskipTests

Esto compilará la aplicación y creará el directorio target.

En este directorio target, se encuentra el jar networkutuils-0.0.1-SNAPSHOT.jar, el cual se ejecuta de la siguiente forma:

Para exportar:
.. code-block:: console

  java -jar networktutils-0.0.1-SNAPSHOT.jar DUMP nombreArchivo.xlsx

Para importar:
.. code-block:: console

  java -jar networktutils-0.0.1-SNAPSHOT.jar UPLOAD nombreArchivo.xlsx

NOTA: se recomienda siempre realizar un respaldo de la tabla networks antes de realizar el UPLOAD.
