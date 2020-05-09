Networkutils (utilitario para modificar redes por lotes)
========================================================

1)En el directorio /home/lareferencia/lrharvester/networkutils/src/main/resources abrir el archivo application.properties.

2)En este archivo deben configurarse los datos para la conexión con la base de datos

spring.datasource.url=jdbc:postgresql://localhost:5432/nombreDeLaBase
spring.datasource.username=usuario
spring.datasource.password=contraseña

3)Luego, en el directorio /home/lareferencia/lrharvester/networkutils debe ejecutarse:

mvn clean package install -DskipTests

Esto compilará la aplicación y creará el directorio target.

4)En este directorio target, se encuentra el jar networkutuils-0.0.1-SNAPSHOT.jar, el cual se ejecuta de la siguiente forma:

Para exportar:
java -jar networktutils-0.0.1-SNAPSHOT.jar DUMP nombreArchivo.xlsx

Para importar:
java -jar networktutils-0.0.1-SNAPSHOT.jar UPLOAD nombreArchivo.xlsx

**IMPORTANTE: se recomienda siempre realizar un respaldo de la tabla networks antes de realizar el UPLOAD.
