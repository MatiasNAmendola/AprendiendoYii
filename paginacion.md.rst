Paginación en Yii
=================

El uso de la paginación en grandes tablas de datos se vuelve una gran
necesidad. Un mecanismo de paginación no solamente permite avanzar o
retroceder entre grupos de registros sino que también permite informar
de la cantidad de registros que conforman el conjunto de datos y sobre
en que grupo se encuentra ubicado el usuario.

Este tutorial muestra un mecanismo sencillo para implementar paginación
utilizando el framework Yii. Igual que antes, se utilizará el ejemplo
del Sistema Universitario de tutoriales anteriores.

Modificando el controlador
--------------------------

La primera modificación a realizar se aplica al controlador de
profesores, en particular a la acción que lista los profesores:

::

    public function actionIndex($start=0,$block=5) {
      $criteria=new CDbCriteria;
      $criteria->limit=$block;
      $criteria->offset=$start;
      $profs=Profesor::model()->findAll($criteria);
      $this->render('profesorListado',array(
          'profs'=>$profs,'start'=>$start,'block'=>$block,
       ));
    }

Como se puede observar, se ha utilizado un nuevo elemento de Yii,
llamado un CDbCriteria que permite definir dos nuevos parámetros de la
consulta SQL. Dichos parámetros consisten de el límite (limit) de
registros a recuperar y el desplazamiento (offset) dentro de dicho
conjunto. Otro aspecto importante aquí es que estos valores son pasados
como parámetros de la acción y tienen valores por omisión.

Modificando la vista
--------------------

Una vez que se ha realizado esta modificación se puede agregar el
mecanismo de paginación a la vista. En este caso se presenta la nueva
versión del archivo *profesorListado.php* con las modificaciones
aplicadas:

::

    <h2>Listado de profesores</h2><br/>
    <input type="button" value="Agregar"
     onclick="redirectLink('?r=profesor/agregar')"/>
    <table border="1">
     <tr><th>Nombre</th><th>Título</th><th>Acción</th></tr>
     <?php foreach ($profs as $prof) { ?>
       <tr><td><?php echo $prof->nomProf ?></td>
       <td><?php echo $prof->tituloProf ?></td>
       <td><a href="?r=profesor/consulta&id=<?php
         echo $prof->idProfesor ?>"/>
         Detalle</a>
       <a href="?r=profesor/actualizar&id=<?php
         echo $prof->idProfesor ?>"/>
         Actualizar</a>
       <a href="?r=profesor/eliminar&id=<?php
         echo $prof->idProfesor ?>"/>
         Eliminar</a>
       </td>
       </tr>
     <?php } ?>
    </table>
    <a href="?r=profesor&start=<?php 
     if($start-$block<0) echo 0; 
     else echo ($start-$block) ?>
     &block=<?php echo $block ?>">
     &lt;Anterior</a>&nbsp&nbsp
    Mostrando del registro <?php echo $start+1 ?> 
       al <?php echo $start+$block?>&nbsp&nbsp
    <a href="?r=profesor&start=<?php 
     echo $start+$block 
      ?>&block=<?php echo $block ?>">
     Posterior&gt;</a>

En este caso es importante observar que los cambios realizados se ubican
al final del archivo y que este código lo que hace es generar dos
enlaces (anterior y posterior) con llamadas a la misma página pero con
valores de *start* y *block* diferentes.

Modificando el layout
---------------------

Un cambio menor, y que en realidad no está asociado con la paginación,
tiene que ver con el botón de *Agregar*. En el código anterior de la
vista se puede notar que se modificó la mecanismo para activar dicho
botón. El nuevo mecanismo utiliza un script para ejecutar la acción.
Dicho script se debe agregar en el código del archivo *main.php* que se
encuentra bajo el directorio /layouts/. La nueva versión de este archivo
sería la siguiente:

::

    <html>
    <head>
    <meta http-equiv="content-type" content="text/html;
       charset=iso-8859-1">
    <title>Sistema Universitario</title>
    </head>
    <link rel="stylesheet" type="text/css" href="css/main.css" />
    <body>
    <script language="JavaScript" type="text/javascript">
     function redirectLink(link) {
       window.location=link
      }
    </script>
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
    </body>
    </html>

Modificando el detalle
----------------------

Otro cambio menor se relaciona al detalle del profesor. En este momento
cuando se muestra el nombre de la escuela a que pertenece el profesor,
no se puede “navegar” a dicha escuela. Modificar el código, del archivo
*profesorDetalle.php*, para incorporar dicha funcionalidad resulta muy
sencillo.:

::

    <?php
     $escuela=Escuela::model()->find('idEscuela = :idEscuela', 
                        array(':idEscuela'=>$prof->idEscuela));
    ?>
    <h2>Detalle de profesor</h2><br/>
    <table>
     <tr><td><b>Ident.:</b></td>
       <td><?php echo $prof->idProfesor ?></td></tr>
     <tr><td><b>Nombre:</b>
       </td><td><?php echo $prof->nomProf ?></td></tr>
     <tr><td><b>Título:</b></td>
       <td><?php echo $prof->tituloProf ?></td></tr>
     <tr><td><b>Escuela:</b></td>
       <td><a href="?r=escuela/consulta&id=<?php
         echo $prof->idEscuela ?>">
       <?php echo $escuela->nombEscuela ?></a></td></tr>
    </table>

