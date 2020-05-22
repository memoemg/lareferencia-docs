Creación de un módulo
---------------------

VuFind is built as a Laminas module. All of its routes and services are configured in /module/VuFind/config/module.config.php.

You can override these settings by building your own custom module. VuFind's install script (php $VUFIND_HOME/install.php) provides the option of initializing a custom module. This establishes a directory and namespace where you can store your local configurations and code. It also sets up the VUFIND_LOCAL_MODULES environment variable that your Apache configuration uses to tell VuFind to load the custom code. Even if you already have VuFind set up, you can run the install script a second time to make adjustments; just be aware that it will create a new Apache configuration in $VUFIND_LOCAL_DIR (while also making a backup of your existing configuration, in case you need to roll back).

Your configuration overrides belong in /module/[your module name]/config/module.config.php. Your custom code belongs in /module/[your module name]/src/[your module name], and it should live in a PHP namespace that corresponds with the module's name. 

Para crear el módulo LAReferencia se debe ejecutar nuevamente el script de install.
Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

.. code-block:: console
  vufind]$ php install.php

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console
  php public/index.php generate extendclass --extendfactory VuFind\\RecordDriver\\SolrDefault LAReferencia

  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefault.php
  Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefaultFactory.php
  Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php
  



 sudo apt-get install php

Habilitación de un SolrDefault distinto
---------------------------------------
