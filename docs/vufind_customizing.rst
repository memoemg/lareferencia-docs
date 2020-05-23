Creación del módulo LAReferencia
--------------------------------

Todas las rutas y servicios de VuFind se encuentran configuradas en /module/VuFind/config/module.config.php.
Es posible sobreescribir estas configuraciones mediante la creación de un módulo personalizado.

Para crear el módulo LAReferencia se debe ejecutar nuevamente el script *install*.

.. code-block:: console
   cd $VUFIND_HOME
   php install.php

**Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

Al definir el nombre del nuevo módulo, en este caso **LAReferencia**, se creará un *namespace* y un directorio en el cual se almacenarán las configuraciones.  Específicamente:

  * Las modificaciones a la configuración se realizan en /module/**LAReferencia**/config/module.config.php. 
  * Las modificaciones al código fuente se realizan en /module/**LAReferencia**/src/**LAReferencia**.

NOTA: El código fuente nuevo debe estar bajo un *namespace* PHP que corresponda con el nombre del módulo.

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console

   php public/index.php generate extendclass --extendfactory VuFind\\RecordDriver\\SolrDefault LAReferencia
   Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefault.php
   Saved file: /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/SolrDefaultFactory.php
   Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php
   Successfully updated /usr/local/vufind/module/LAReferencia/config/module.config.php


Mostrar un campo nuevo en el Record
-----------------------------------

En VuFind todos los *displays* se realizan mediante las plantillas PHP.

La información utilizada por estas plantillas es obtenida por *RecordDriver*, la cual es una clase con muchos métodos públicos que recuperan la información en su mayoría desde el índice SOLR.

*RecordDriver* obtiene la información de los campos dentro del índice a través de sus *fields*.

Para hacer visible un nuevo campo en la vista del Record es necesario:

  * Hacer disponible el dato para VuFind
  * Hacer el dato accesible através de un *RecordDriver*
  * Desplegar el dato en la vista del *record*

1)Hacer disponible el dato para VuFind:

El nuevo dato debe estar indexado en el índice SOLR.  Debe corroborarse que se encuentre en el schema.xml.

2) Hacer el dato accesible através de un *RecordDriver*:

Si el campo es totalmente nuevo y desconocido para VuFind, es necesario programar un método get específico para su recuperación.  Este método debe agregarse a la clase SolrDefault.php del módulo.  

En el caso del módulo LAReferencia el archivo se encuentra en /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/ y se llama SolrLAReferencia.php

.. code-block:: php

   <?php
   namespace LAReferencia\RecordDriver;
   class SolrLAReferencia extends SolrDefault
   {
   public function getCountry()
   {
      $value = null;
      $value = $this->fields["network_name_str"];
      return $value;
   }

3) Desplegar el dato en la vista del *record*:

Finalmente para que este nuevo campo se visualice en la página del *record*, debe modificarse el *RecordDataFormatter*.

Para lograrlo se crea el siguiente directorio:

.. code-block:: console
   mkdir -p $VUFIND_HOME/module/LAReferencia/src/LAReferencia/View/Helper/Root

Luego se crea el siguiente archivo $VUFIND_HOME/module/LAReferencia/src/LAReferencia/View/Helper/Root/**RecordDataFormatterFactory.php** y se agrega el llamado al método get que se incluyó en el SolrLAReferencia.php:

.. code-block:: php

   <?php
   namespace LAReferencia\View\Helper\Root;
   use VuFind\View\Helper\Root\RecordDataFormatter\SpecBuilder;
   class RecordDataFormatterFactory extends \VuFind\View\Helper\Root\RecordDataFormatterFactory
   {
        public function getDefaultCoreSpecs()
      {
            $spec = new SpecBuilder(parent::getDefaultCoreSpecs());
            $spec->setLine('País', '**getCountry**');
            return $spec->getArray();
        }
   }

Lo último que debe realizarse es editar el archivo $VUFIND_HOME/module/LAReferencia/config/module.config.php de forma que se incluya específicamente el archivo SolrLAReferencia para uso del módulo:

.. code-block:: php

   <?php
   return array (
   'vufind' => 
   array (
       'plugin_managers' => 
      array (
         'recorddriver' => 
         array (
         'factories' => 
         array (
             'LAReferencia\\RecordDriver\\SolrDefault' => 'LAReferencia\\RecordDriver\\SolrDefaultFactory',
           ),
           'aliases' => 
           array (
             'VuFind\\RecordDriver\\SolrDefault' => **'LAReferencia\\RecordDriver\\SolrLAReferencia'**,
           ),
         ),
      ),
   ),
   );

Habilitación de un SolrDefault distinto
---------------------------------------
