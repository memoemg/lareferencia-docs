Creación de un módulo
---------------------

Todas las rutas y servicios de VuFind se encuentran configuradas en /module/VuFind/config/module.config.php.
Es posible sobreescribir estas configuraciones mediante la creación de un módulo personalizado.

Para crear el módulo LAReferencia se debe ejecutar nuevamente el script *install*.

.. code-block:: console
  php install.php

**Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

Al definir el nombre del nuevo módulo, se creará un *namespace* y un directorio en el cual se almacenarán las configuraciones.  Específicamente:

  * Las modificaciones en la configuración se realizarán en /module/nombreDelModulo/config/module.config.php. 
  * Las modificaciones al código fuente se realizarán en /module/nombreDelModulo/src/nombreDelModulo.

NOTA: El código fuente nuevo debe estar bajo un *namespace* PHP que corresponda con el nombre del módulo.

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console

  php public/index.php generate extendclass --extendfactory VuFind\\RecordDriver\\SolrDefault LAReferencia
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefault.php
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefaultFactory.php
  Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php

Mostrar un campo nuevo en el Record
-----------------------------------

En VuFind todos los *displays* se realizan mediante las plantillas PHP de cada tema.

La información utilizada por estas plantillas es obtenida por *RecordDriver*, la cual es una clase con muchos métodos públicos que recuperan la información en su mayoría desde el índice SOLR.

*RecordDriver* obtiene la información de los campos dentro del índice a través de sus *fields*.

Para hacer visible un nuevo campo en la vista del Record es necesario:

  * Hacer disponible el dato para VuFind
  * Hacer el dato accesible através de un *RecordDriver*
  * Desplegar el dato en el template

1)Hacer disponible el dato para VuFind:
El nuevo dato debe estar indexado en el índice SOLR.  Debe corroborarse que se encuentre en el schema.xml.

2) Hacer el dato accesible através de un *RecordDriver*:
Al agregar un nuevo campo es necesario agregar un getter method al record driver utilizado para desplegar los records.
SolrDefault.  Al copiar un método de los ya existentes, debe tenerse en cuenta el detalle del return value: asegurarse si retorna un string cuando es single-valued y devolver un array cuando es multi-valued (propiedad del solr field).

3) Desplegar el dato en el template apropiado
El paso final es hacerlo visible a través del template deseado.  "Record-relate templates are found in the RecordDriver folder of your chosen theme in a folder that corresponds with the name of the record driver class"

Habilitación de un SolrDefault distinto
---------------------------------------
