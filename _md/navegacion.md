Organizando la navegación en Yii
================================

El framework Yii cuenta con varias facilidades para crear un diseño de página adecuado. Eso incluye capacidades para creación de menús, migas de pan y paginación de datos. Sin embargo, muchas veces esa misma funcionalidad se puede lograr con unas pocas líneas de código.

Continuando con los tutoriales sobre el uso de Yii, se presentan ahora varios mecanismos para mejorar la apariencia y navegación del sistema universitario.

Mejorando la apariencia
-----------------------

El uso de hojas de estilo permite mejorar mucho la apariencia de las páginas web. Generalmente, una hoja de estilo consta de diferentes secciones que especifican la apariencia de cada uno de los elementos que conforman una página web.

Generalmente una página web consistirá de tres elementos principales: el encabezado (header), el contenido (content) y el pie de página (footer). El siguiente archivo de hoja de estilo, llamado *main.css* y que se ubica en el directorio universidad/css, muestra la especificación de dichos elementos con una apariencia particular:

    body {
     margin: 0;
     padding: 0;
     color: #555;
     font: normal 10pt Arial,Helvetica,sans-serif;
     background: #EFEFEF;
    }
    #container {
     margin: 0 auto;
     width: 100%;
     background: #fff;
    }
    #header {
     background: #ccc;
     padding: 20px;
    }
    #header h1 { margin: 0; }
    #content-container {
     float: left;
     width: 100%;
     background: #FFF url(bg.gif) repeat-y 68% 0;
    }
    #content {
     clear: left;
     float: left;
     width: 60%;
     padding: 20px 0;
     margin: 0 0 0 4%;
     display: inline;
    }
    #content h2 { margin: 0; }
    #footer {
     clear: left;
     background: #ccc;
     text-align: center;
     padding: 20px;
     height: 1%;
    }

La imagen *bg.gif* puede descargarse desde [http://www.arcux.com/public/bg.gif](http://www.arcux.com/public/bg.gif)

Para que esta hoja de estilo sea utilizada por la aplicación, es necesario modificar el archivo de layout que se utiliza. A continuación se presenta otra versión del archivo *main.php* que se ubica en el directorio *universidad/protected/views/layouts/*, en donde se muestra el uso de los estilos:

    <meta http-equiv="content-type" content="text/html; charset=iso-8859-1">
    <title>Sistema Universitario</title>
    </head>
    <link rel="stylesheet" type="text/css" href="css/main.css" />
    <body>
    <div class="container">
     <div id="header">
       <h1>Sistema de Información Universitario</h1>
     </div>
     <div id="content-container">
       <div id="content">
         <?php echo $content; ?>
       </div>
       <div id="footer">
          Universidad del Sol - Derechos Reservados
       </div>
     </div>
    </div>
    </body></html>

La nueva apariencia se puede observar al invocar el enlace [http://localhost/universidad](http://localhost/universidad).

Creando el menú principal
-------------------------

Un elemento que no puede faltar en una aplicación web es el menú principal. Dicho menú debe incluir un enlace que permita acceder a las secciones principales. En este caso se utilizará una lista html sencilla para definir las diferentes entradas del menú. Adicionalmente se modificará la hoja de estilo para que cambie la apariencia de la lista y muestra las opciones en forma horizontal.

Las especificaciones adicionales para la hoja de estilo, llamada *main.css* se presentan a continuación:

    #navigation {
     float: left;
     width: 100%;
     background: #333;
    }
    #navigation ul {
     margin: 0;
     padding: 0;
    }
    #navigation ul li {
     list-style-type: none;
     display: inline;
    }
    #navigation li a {
     display: block;
     float: left;
     padding: 5px 10px;
     color: #fff;
     text-decoration: none;
     border-right: 1px solid #fff;
    }
    #navigation li a:hover { background: #383; }

Ahora, se debe crear una nueva versión del layout que incluya el menú principal en cada página de la aplicación. En el código que se presenta a continuación se puede observar cómo se especifica dicho menú y cómo se hace referencia a la sección mainmenu de la hoja de estilo. Se han incluido los enlaces a las páginas creadas en anteriores tutoriales:

    <meta http-equiv="content-type" content="text/html; charset=iso-8859-1"> 
    <title>Sistema Universitario</title>
    </head>
    <link rel="stylesheet" type="text/css" href="css/main.css" />
    <body>
    <div class="container">
     <div id="header">
       <h1>Sistema de Información Universitario</h1>
     </div>
     <div id="navigation">
       <ul>
         <li><a href="?r=site/index">Inicio</a></li>
         <li><a href="?r=escuela">Escuelas</a></li>
         <li><a href="?r=profesor">Profesores</a></li>
         <li><a href="?r=site/contacto">Contacto</a></li>
         <li><a href="?r=ayuda/index">Ayuda</a></li>
       </ul>
     </div>
     <div id="content-container">
       <div id="content">
         <?php echo $content; ?>
       </div>
       <div id="footer">
          Universidad del Sol - Derechos Reservados
       </div>
     </div>
    </div>
    </body></html>

La nueva apariencia del sitio, incorporando el menún principal puede observar al invocar el enlace [http://localhost/universidad](http://localhost/universidad).

Creando un menú secundario
--------------------------

Una vez que se accede a una sección del sitio, existen varias opciones que se pueden ejecutar en dicho lugar. Estas opciones deben ser presentadas en un menú secundario que normalmente se ubica a la izquierda o derecha de la pantalla. Debido a que no son todas las áreas las que requerirán de un menú secundario, es necesario indicar en el controlador el uso de un layout adicional.

El siguiente código muestra la forma de definir un layout adicional en un controlador. Dicho layout se llama *escuelaLayout*. En este caso se utiliza el controlador *EscuelaController.php*

    <?php
     class EscuelaController extends CController {
      public $layout='//layouts/escuelaLayout';
      public function actionIndex() {
        $escuelas=Escuela::model()->findAll();
        $this->render('escuelaListado',array(
          'escuelas'=>$escuelas,
        ));
      }
      public function actionConsulta($id) {
        $escuela=Escuela::model()->find('idEscuela=:idEscuela',
                                  array(':idEscuela'=>$id));
        $profs=$escuela->escuela2profesor;
        $this->render('escuelaDetalle',array(
          'escuela'=>$escuela,
          'profs'=>$profs,
       ));
      }
      public function actionBuscar() {
        $model = new BuscarEscuelaForm;
        $form = new CForm('application.views.escuela.escuelaBuscar',$model);
        if ($form->submitted('buscar')&& $form->validate()) {
          $nombre = $model->nombre;
          $this->redirect(array('resultados'."&nombre=".$nombre));
        }
        else
          $this->render('buscarForm',array(
            'form'=>$form,
        ));
      }
      public function actionResultados($nombre) {
        $escuela=Escuela::model()->find('nombEscuela LIKE :nombEscuela',
                                  array(':nombEscuela'=>'%'.$nombre.'%'));
        $profs=$escuela->escuela2profesor;
        $this->render('escuelaDetalle',array(
          'escuela'=>$escuela,
          'profs'=>$profs,
         ));
      }
     }

El layout *escuelaLayout.php* reside en el directorio *universidad/protected/views/layouts/* y su código podría ser como el siguiente:

    <?php $this->beginContent('//layouts/main'); ?>
     <?php echo $content; ?>
     </div>
     <div id="aside">
       <h3>Menú local</h3>
       <ul>
         <li><a href="?r=escuela/buscar">Buscar escuela</a></li>
         <li><a href="?r=escuela">Lista de escuelas</a></li>
       </ul>
     </div>
    <?php $this->endContent(); ?>

También, se debe agregar las siguientes definiciones al archivo *main.css* para poder mostrar el menú secundario:

    #aside {
     float: right;
     width: 26%;
     padding: 20px 0;
     margin: 0 3% 0 0;
     display: inline;
    }
    #aside h3 { margin: 0; }

Basta con invocar el siguiente enlace para observar el uso del menú secundario [http://localhost/universidad/?r=escuela](http://localhost/universidad/?r=escuela)
