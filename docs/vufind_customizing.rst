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

Debe ejecutar el siguiente comando

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

Para hacer visible un nuevo campo en la vista del *record* es necesario:

  * Hacer disponible el dato para VuFind
  * Hacer el dato accesible através de un *RecordDriver*
  * Desplegar el dato en la vista del *record*

1) Hacer el dato disponible para VuFind:

El nuevo dato debe estar indexado en el índice SOLR.  Debe corroborarse que se encuentre en el archivo $VUFIND_HOME/solr/vufind/biblio/conf/schema.xml

.. code-block:: xml

   <!-- Generic Fields -->
      <field name="language" type="string" indexed="true" stored="true" multiValued="true"/>
      <field name="format" type="string" indexed="true" stored="true" multiValued="true"/>
      <field name="author" type="textProper" indexed="true" stored="true" multiValued="true" termVectors="true"/>
      <field name="author_variant" type="text" indexed="true" stored="true" multiValued="true" termVectors="true"/>
      <field name="author_role" type="string" indexed="true" stored="true" multiValued="true"/>
      <field name="author_facet" type="textFacet" indexed="true" stored="true" multiValued="true"/>
      <field name="author_sort" type="string" indexed="true" stored="true"/>
      <field name="title" type="text" indexed="true" stored="true"/>
      <field name="title_sort" type="string" indexed="true" stored="true"/>
      <field name="title_sub" type="text" indexed="true" stored="true"/>
      <field name="title_short" type="text" indexed="true" stored="true"/>
      <field name="title_full" type="text" indexed="true" stored="true"/>
      <field name="title_full_unstemmed" type="textProper" indexed="true" stored="true"/>
      <field name="title_fullStr" type="string" indexed="true" stored="true"/>
      <field name="title_auth" type="text" indexed="true" stored="true"/>
      <field name="physical" type="string" indexed="true" stored="true" multiValued="true"/>
      <field name="publisher" type="textProper" indexed="true" stored="true" multiValued="true"/>
      <field name="publisherStr" type="string" indexed="true" stored="false" multiValued="true"/>
      <field name="publishDate" type="string" indexed="true" stored="true" multiValued="true"/>
      <field name="publishDateSort" type="string" indexed="true" stored="false"/>
      <field name="edition" type="string" indexed="true" stored="true"/>
      <field name="description" type="text" indexed="true" stored="true"/>
      <field name="contents" type="text" indexed="true" stored="true" multiValued="true"/>
      <field name="url" type="string" indexed="false" stored="true" multiValued="true"/>
      <field name="thumbnail" type="string" indexed="false" stored="true"/>

Si este no forma parte del schema.conf debe entonces agregarse al índice por otro medio, por ejemplo mediante el archivo xoai2vufind4.xsl.

.. code-block:: xml

   <field name="network_name_str">
      <xsl:value-of select="$networkName"/>
   </field>

2) Hacer el dato accesible através de un *RecordDriver*:

Si el campo es totalmente nuevo y desconocido para VuFind, es necesario programar un método get específico para su recuperación.  Este método debe agregarse a la clase SolrDefault.php del módulo.  

En el caso del módulo LAReferencia el archivo se encuentra en $VUFIND_HOME/module/LAReferencia/src/LAReferencia/RecordDriver/ y se llama SolrLAReferencia.php

.. code-block:: php

   <?php
   namespace LAReferencia\RecordDriver;
   class SolrLAReferencia extends SolrDefault
   {
   public function getCountry()
   {
      return $this->getFieldValue("network_name_str");
   }
 
3) Desplegar el dato en la vista del *record*:

Finalmente para que este nuevo campo se visualice en la página del *record*, debe modificarse el *RecordDataFormatter*.  Este es el responsable de la forma en la que se muestran los metadatosn del *record*.

Para lograrlo se crea el siguiente directorio:

.. code-block:: console
   mkdir -p $VUFIND_HOME/module/LAReferencia/src/LAReferencia/View/Helper/Root

Luego se crea el siguiente archivo $VUFIND_HOME/module/LAReferencia/src/LAReferencia/View/Helper/Root/**RecordDataFormatterFactory.php** y se agrega el llamado al método get que se incluyó en el SolrLAReferencia.php:

.. code-block:: php

 <?php
 
 namespace ModuleName\View\Helper\Root;
 
 use VuFind\View\Helper\Root\RecordDataFormatter\SpecBuilder;
 
 class RecordDataFormatterFactory extends \VuFind\View\Helper\Root\RecordDataFormatterFactory
 {
     public function getDefaultCoreSpecs()
  {
      $spec = new RecordDataFormatter\SpecBuilder();
      $spec->setTemplateLine(
          'Published in', 'getContainerTitle', 'data-containerTitle.phtml'
      );
      $spec->setLine(
          'New Title', 'getNewerTitles', null, ['recordLink' => 'title']
      );

      $spec->setLine(
          'Country', 'getCountry');
      
      $spec->setLine(
          'Access Level', 'getAccessLevel');

      $spec->setLine(
          'Previous Title', 'getPreviousTitles', null, ['recordLink' => 'title']
      );

      $spec->setLine(
          'Publication Date', 'getPublicationDates');
 }
   
El código anterior incluye una nueva línea en el *display* de los metadatos, con la etiqueta "Country" y el valor regresado por la función getCountry.

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
                'VuFind\\RecordDriver\\SolrDefault' => 'LAReferencia\\RecordDriver\\SolrLAReferencia',
             ),
           ),
         ),
      ),
   );

Habilitación de un SolrDefault distinto
---------------------------------------

Si dentro del mismo módulo se poseen diferentes implementaciones de la clase SolrDefault, por ejemplo un archivo SolrLAReferencia.php y otro SolrIBCT.php.  Es posible cambiar entre ellos y de esta forma cambiarán la implementación de los *gets* de las funciones invocadas en RecordDataFormatterFactory para mostrar en la vista del *record*.  

Para realizar esta habilitación basta con cambiar el nombre del archivo llamado en module.config.php

.. code-block:: php

   <?php
   ...
   'aliases' => 
      array (
         'VuFind\\RecordDriver\\SolrDefault' => 'LAReferencia\\RecordDriver\\SolrIBICT',
         

Luego debe llamarse al archivo RecordDataFormatter correspondiente desde el archivo theme.config.php en el tema utilizado.  Por ejemplo:

.. code-block:: php

   <?php
   return [
       'extends' => 'bootstrap3',
       'helpers' => ['factories' => ['VuFind\View\Helper\Root\RecordDataFormatter' => 'LAReferencia\View\Helper\Root\RecordDataFormatterFactoryIBICT']],
   ];
