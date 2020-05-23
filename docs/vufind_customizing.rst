Creación de un módulo
---------------------

Todas las rutas y servicios de VuFind se encuentran configuradas en /module/VuFind/config/module.config.php.
Es posible sobreescribir estas configuraciones mediante la creación de un módulo personalizado.

Para crear el módulo LAReferencia se debe ejecutar nuevamente el script *install*.

.. code-block:: console
   cd $VUFIND_HOME
   php install.php

**Precaución, si ha modificado el archivo httpd-vufind.conf los cambios se perderan y un archivo .bak será creado.

Al definir el nombre del nuevo módulo, se creará un *namespace* y un directorio en el cual se almacenarán las configuraciones.  Específicamente:

  * Las modificaciones en la configuración se realizarán en /module/nombreDelModulo/config/module.config.php. 
  * Las modificaciones al código fuente se realizarán en /module/nombreDelModulo/src/nombreDelModulo.

NOTA: El código fuente nuevo debe estar bajo un *namespace* PHP que corresponda con el nombre del módulo.

Extensión de la clase SolrDefault
---------------------------------

.. code-block:: console

  php public/index.php generate extendclass VuFind\\RecordDriver\\SolrMarc ModuleName
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

Si el campo es totalmente nuevo y desconocido para VuFind, es necesario programar un método get específico para su recuperación.  Este método debe agregarse a la clase SolrDefault.php del módulo.  

En el caso del módulo LAReferencia el archivo se encuentra en /usr/local/vufind/module/LAReferencia/src/LAReferencia/RecordDriver/ y se llama SolrLAReferencia.php

.. code-block:: console

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

3) Desplegar el dato en el template apropiado

 <?php
  namespace ModuleName\View\Helper\Root;
  use VuFind\View\Helper\Root\RecordDataFormatter\SpecBuilder;
  class RecordDataFormatterFactory extends \VuFind\View\Helper\Root\RecordDataFormatterFactory
 {
     public function getDefaultCoreSpecs()
     {
         $spec = new SpecBuilder(parent::getDefaultCoreSpecs());
         $spec->setLine('Record ID', 'getRecordID');
         return $spec->getArray();
     }
 }
 
 <?php
 return [
     'extends' => 'bootstrap3',
     'helpers' => ['factories' => ['VuFind\View\Helper\Root\RecordDataFormatter' => 'ModuleName\View\Helper\Root\RecordDataFormatterFactory']],
 ];

Habilitación de un SolrDefault distinto
---------------------------------------
