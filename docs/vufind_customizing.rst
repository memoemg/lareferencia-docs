Creación de un módulo
---------------------

Todas las rutas y servicios de VuFind se encuentran configuradas en /module/VuFind/config/module.config.php.
Es posible sobreescribir estas configuraciones mediante la creación de un módulo personalizado.

Para crear el módulo LAReferencia se debe ejecutar nuevamente el script *install*.

.. code-block:: console
  php install.php

**Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

Al definir el nombre del nuevo módulo, se creará un *namespace* y un directorio en el cual se almacenarán las configuraciones.
Las modificaciones en la configuración se realizarán en /module/nombreDelModulo/config/module.config.php. 
Las modificaciones al código fuente se realizarán en /module/nombreDelModulo/src/nombreDelModulo.

NOTA: El código fuente nuevo debe estar bajo un *namespace* PHP que corresponda con el nombre del módulo.

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console

  php public/index.php generate extendclass --extendfactory VuFind\\RecordDriver\\SolrDefault LAReferencia
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefault.php
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefaultFactory.php
  Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php

Incluyendo un campo más en el vufind

Todos los displays se realizan mediante las plantillas php de cada tema.
La información utilizada por estas plantillas es obtenida por el "RECORD DRIVER", la cual es una clase con muchos métodos públicos.
"RECORD DRIVER" recupera esta información en su mayoría desde el índice SOLR.

La información de los campos dentro del índice se hace disponible al "RECORD DRIVER" a través de su "fields property"

Record Driver: los record drivers son utilizados para permitirle a VuFind interactuar con metadatos arbitrarios.  Cada record es representado por un "Driver Object" que envuelve todos los datos crudos (ya sea que estos provengan desde un índice Solr, desde una respuesta API o desde una base de datos).  Los record drivers implementan interfaces que permiten que otro código haga uso de esa información sin importarle su origen.



Default Namespace: \VuFind\RecordDriver
Base Class: \VuFind\RecordDriver\AbstractBase

Service Locator Configuration Section in module.config.php: ['vufind']['plugin_managers']['recorddriver'] 

DefaultRecord:

Record data formatter:

Pasos para desplegar un nuevo campo:
1) Hacer disponible el dato para VuFind
2) Hacer el dato accesible através de un record driver
3) Desplegar el dato en el template apropiado


1)Hacer disponible el dato para VuFind
Revisar el schema.xml.

2) Hacer el dato accesible através de un record driver
Al agregar un nuevo campo es necesario agregar un getter method al record driver utilizado para desplegar los records.
SolrDefault.  Al copiar un método de los ya existentes, debe tenerse en cuenta el detalle del return value: asegurarse si retorna un string cuando es single-valued y devolver un array cuando es multi-valued (propiedad del solr field).

3) Desplegar el dato en el template apropiado
El paso final es hacerlo visible a través del template deseado.  "Record-relate templates are found in the RecordDriver folder of your chosen theme in a folder that corresponds with the name of the record driver class"

Habilitación de un SolrDefault distinto
---------------------------------------
