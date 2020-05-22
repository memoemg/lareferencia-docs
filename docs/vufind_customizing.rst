Creación de un módulo
---------------------

Todas las rutas y servicios de VuFind se encuentran configuradas en /module/VuFind/config/module.config.php.
Es posible sobreescribir estas configuraciones mediante la creación de un módulo personalizado.
Para crear el módulo LAReferencia se debe ejecutar nuevamente el script *install*.

.. code-block:: console
  vufind]$ php install.php

**Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

Al definir el nombre del nuevo módulo, se creará un *namespace* y un directorio en el cual se almacenarán las configuraciones.
Las modificaciones en la configuración se realizarán en /module/[nombre del módulo]/config/module.config.php. 
Las modificaciones al código fuente se realizarán en /module/[nombre del módulo]/src/[nombre del módulo].

*NOTA: El código fuente nuevo debe estar bajo un *namespace* PHP que corresponda con el nombre del módulo.

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console
  php public/index.php generate extendclass --extendfactory VuFind\\RecordDriver\\SolrDefault LAReferencia

  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefault.php
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefaultFactory.php
  Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php

Habilitación de un SolrDefault distinto
---------------------------------------
