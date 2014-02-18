Primeros pasos con Yii
======================

De acuerdo a Wikipedia, “un framework es una estructura conceptual y tecnológica de soporte definida, normalmente con artefactos o módulos de software concretos, con base en la cual otro proyecto de software puede ser organizado y desarrollado. Típicamente, puede incluir soporte de programas, bibliotecas y un lenguaje interpretado entre otros programas para ayudar a desarrollar y unir los diferentes componentes de un proyecto.”

Entre los diferentes framework disponibles para el lenguaje PHP se encuentra Yii. Yii es de código abierto, orientado a objetos y basado en componentes. El software se puede descargar desde www.yiiframework.com en donde está disponible la versión 1.1.4.

A continuación, se presenta el primero de una serie de tutoriales sobre el desarrollo de aplicaciones con Yii, para ello se utilizará el caso del Sistema Universitario y se presentarán en forma incremental cada uno de los diferentes elementos que componen una aplicación en este ambiente.

Instalación
-----------

Antes de empezar a desarrollar con Yii es necesario contar con un servidor web que posea capacidades para ejecutar PHP y disponibilidad de bases de datos. Un opción es utilizar un servidor “lite” como Nginx con una implementación mínima de PHP tal como la que se puede descargar desde este enlace [http://www.arcux.com/sitiosweb/blog/public/files/nginx.zip](http://www.arcux.com/sitiosweb/blog/public/files/nginx.zip) (Para que esta distribución funcione adecuadamente debe instalarse en la raíz del disco C).

Una vez que el servidor web se encuentra en ejecución, basta con desempacar el software en un directorio accesible mediante el servidor ( /root ó /html en el caso de nginx). Si la instalación se realiza en la máquina local su url será [http://localhost](http://localhost).

Con el fin de ubicar los diferentes archivos que conformarán la aplicación a desarrollar, es necesario crear una estructura de directorios. Dicha estructura debe estar ubicada en el directorio “root”. En este caso se recomienda la siguiente estructura para desarrollar la aplicación de ejemplo:

    universidad/
     css/
     protected/
       config/
       controllers/
       data/
       models/
       views/
         ayuda/
         escuela/
         layouts/
         profesor/
         site/

Archivos de definición iniciales
--------------------------------

Para crear una primera versión de la aplicación se requiere únicamente la utilización de cinco archivos básicos. El primero de ellos consiste del index.php que se ubicará bajo el directorio universidad/. Dicho programa hace la inclusión del código de la biblioteca yii y el archivo de configuración llamado main.php. Luego crea un objeto del tipo Aplicación Web y lo ejecuta, tal como se puede ver a continuación:

    <?php
    $yii=dirname(__FILE__).'/../framework/yii.php';
    $config=dirname(__FILE__).'/protected/config/main.php';
    require_once($yii);
    Yii::createWebApplication($config)->run();

El archivo de configuración permite especificar una serie de componentes y opciones de la aplicación. En este caso se creará un archivo main.php mínimo que se ubica en el directorio universidad/protected/config/, tal como se muestra aquí:

    <?php
    return array(
      'name'=>'Sistema Universitario',
      'defaultController'=>'site',
      'layout'=>'main',
    );

Como se puede observar el controlador por omisión de la Aplicación Web es llamado site, por lo que debe existir un archivo llamado SiteController.php que se ubicaría en el directorio universidad/protected/controllers/. El código mínimo de dicho controlador podría ser el siguiente:

    <?php
    class SiteController extends CController {
      public function actionIndex() {
        $this->render('principal');
      }
    }

En el código anterior se puede notar que actionIndex es la acción por omisión que ejecuta todo controlador. En este caso dicha acción ejecuta a su vez un llamado para desplegar (render) la vista llamada “inicial”. El código de dicha vista se encontrará en el archivo principal.php que estará ubicado en el directorio universidad/protected/views/site/, y que podría ser cualquier contenido html que se quiera mostrar, tal como el que aparece a continuación:

    <h2>Bienvenido</h2>
    <p>Página principal del Sistema de Información Universitaria</p>

Aquí es importante indicar que el archivo tipo vista únicamente se debe encargar de generar el contenido de la página. Este no debe preocuparse del formato de la misma. La apariencia, distribución y formato de la página son responsabilidad de otro archivo. En el archivo de configuración main.php se puede observar que el layout a utilizar se llama también main pero se asocia con un archivo llamado main.php que esta vez se ubica en el directorio universidad/protected/views/layouts/ y cuyo código mínimo podría ser el siguiente:

    <html>
    <head>
    <meta http-equiv="content-type" content="text/html; charset=UTF8">
    <title>Sistema Universitario</title>
    </head>
    <body><?php echo $content; ?></body>
    </html>

Se puede observar el resultado de este código abriendo la dirección:

::

[http://localhost/universidad](http://localhost/universidad)

Creando nuevas vistas
---------------------

Para crear otras páginas de contenido estático se debe modificar el controlador para que responda a nuevas acciones. Se modificará el archivo SiteController.php para incluir una nueva acción que muestre una segunda página, tal como aparece a continuación:

    <?php
    class SiteController extends CController {
      public function actionIndex() {
        $this->render('principal');
      }
      public function actionContacto() {
        $this->render('contacto');
      }  
    }

Es necesario contar con una nueva vista llamada contacto.php que se encargue de generar el contenido de esta otra página. Al igual que antes el archivo se debe ubicar en el directorio universidad/protected/views/site/ y su contenido podría ser el que se muestra a continuación:

    <h2>Página de contacto</h2>
    <p>Aquí puede encontrar información de contacto</p>

Ahora, para observar el resultado de esta segunda página se debe incluir el nombre del controlador y de la nueva acción en un parámetro del url de la siguiente forma:

::

[http://localhost/universidad?r=site/contacto](http://localhost/universidad?r=site/contacto)

En el caso anterior se utiliza un mismo controlador para ambas vistas. Es también posible contar con otros controladores que administren sus propias vistas. La creación de dichos controladores resulta similar al controlador principal. Ahora se creará un controlador para la sección de ayuda del sitio, el nombre del archivo será AyudaController.php el cuál debe estar ubicado en el directorio universidad/protected/controllers/. El código podría ser el siguiente:

    <?php
    class AyudaController extends CController {
      public function actionIndex() {
        $this->render('inicial');
      }
    }

Es necesario crear un nuevo directorio llamado universidad/protected/views/ayuda/ en donde se debe incluir el archivo inicial.php tal como aparece a continuación:

    <h2>Sección de ayuda</h2>
    <p>Página inicial de la sección de ayuda</p>

La forma de invocar este controlador sería la siguiente:

::

[http://localhost/universidad?r=ayuda](http://localhost/universidad?r=ayuda)

Transferencia de datos entre el controlador y la vista
------------------------------------------------------

Lo normal es que el controlador genere datos que serán pasados a la vista para que ésta a su vez genere el contenido adecuado. Para realizar esto, es necesario el paso de parámetros desde el controlador a la hora de invocar al despliegue de la vista. Mediante un arreglo de parámetros se logra pasar todos los valores adecuados a la vista. El siguiente código es una nueva versión del controlador SiteController.php que muestra un ejemplo de la transferencia de datos a la vista:

    <?php
    class SiteController extends CController {
      public function actionIndex() {
        $fecha = date("F j, Y, g:i a");
        $this->render('principal',array(
          'fecha'=>$fecha,
        ));
      }
      public function actionContacto() {
        $correo = 'sistema@xyy.com';
        $telefono = '234-6789';
        $this->render('contacto',array(
          'email'=>$correo,
          'telef'=>$telefono,
        ));
      }
    }

Ahora es necesario rescribir las vistas para que obtengan los parámetros. En el caso de la primera vista, llamada principal.php se incluye la fecha en dicha página, como se muestra a continuación:

    <h2>Bienvenido</h2>
    <p>Página principal del Sistema de Información Universitaria</p>
    <p>La fecha de hoy es <?php echo $fecha ?></p>

La segunda vista, llamada contacto.php, debe ser modificada para incluir la nueva información que se pasa por parámetros. El código de dicha página sería similar al siguiente:

    <h2>Página de contacto</h2>
    <p>Aquí puede encontrar información de contacto</p>
    <p>Correo electrónico: <?php echo $email ?></p>
    <p>Teléfono: <?php echo $telef ?></p>

Recibiendo parámetros desde el URL
----------------------------------

Un controlador puede recibir parámetros a través del URL que lo invoca. Existen dos métodos que se pueden utilizar para recuperar dichos parámetros. El primero utiliza la variable de ambiente $\_GET y extrae los datos conforme los requiera. El siguiente ejemplo muestra el código de la nueva acción actionBuscar, que pertenece al archivo AyudaController.php, en donde se puede observar el uso de este mecanismo:

    public function actionBuscar() {
      $tema = $_GET['tema'];
      $this->render('buscar',array(
        'tema'=>$tema,
      ));
    }

Se requiere ahora la creación de una nueva vista llamada buscar.php y que residirá en el directorio universidad/protected/views/ayuda/. El código de este archivo será el siguiente:

    <h2>Página de búsqueda de ayuda</h2>
    <p>Usted está buscando ayuda sobre este tema:
      <?php echo $tema ?></p>

Esta acción se puede invocar mediante la solicitud:

::

[http://localhost/universidad/?r=ayuda/buscar&tema=matricula](http://localhost/universidad/?r=ayuda/buscar&tema=matricula)

El segundo mecanismo consiste en utilizar parámetros en la acción. Esto resulta más natural a la hora de programar y hace que el código de la acción luzca más limpio. Ahora se presenta un nueva versión del controlador AyudaController.php que incluye la nueva acción de buscar pero esta vez utilizando el parámetro en la acción:

    <?php
    class AyudaController extends CController {
      public function actionIndex() {
        $this->render('inicial');
      }
      public function actionBuscar($tema) {
        $this->render('buscar',array(
          'tema'=>$tema,
        ));
      }
    }

Es importante indicar que los nombres de los parámetros deben ser exactamente iguales a aquellos utilizados en el método que utiliza $\_GET.
