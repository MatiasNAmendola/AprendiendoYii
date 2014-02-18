Inclusión y modificación de datos en Yii
========================================

El framework Yii cuenta con capacidad para realizar la inclusión y
modificación de datos de una forma sencilla y rápida. Continuando con el
tutorial anterior, esta vez se presentarán las modificaciones necesarias
al sistema universitario que permitan la inclusión y modificación de
datos de profesores.

Agregando datos
---------------

El primer archivo que se modificará será la vista del listado de
profesores, llamada *profesorListado.php* y que se ubica en el
directorio *protected/views/profesor*. En este caso únicamente se
agregará un botón al inicio de la tabla que permita invocar a la acción
de agregar tal como se muestra a continuación:

::

    <h2>Listado de profesores</h2><br/>
    <a href="?r=profesor/agregar"/>
     <input type="button" value="Agregar"/></a>
    <table border="1">
     <tr><th>Nombre</th><th>Título</th><th>Acción</th></tr>
     <?php foreach ($profs as $prof) { ?>
       <tr><td><?php echo $prof->nomProf ?></td>
       <td><?php echo $prof->tituloProf ?></td>
       <td><a href="?r=profesor/consulta&id=<?php
         echo $prof->idProfesor ?>"/>
         Detalle</a>
       </td>
       </tr>
     <?php } ?>
    </table>

La acción de agregar se debe incluir en el controlador de profesor,
llamado *ProfesorController.php* y ubicado en el directorio
*/protected/controllers*. Esta acción utiliza un formulario y una vista
adicionales tal como se muestra en el siguiente código:

::

    public function actionAgregar() {
      $prof = new Profesor;
      $model = new ActualizarProfesorForm;
      $form = new CForm('application.views.profesor.profesorActualizar',$model);
      if ($form->submitted('guardar')&& $form->validate()) {
        $prof->nomProf = $model->nombProf;
        $prof->idProfesor = $model->idProf;
        $prof->tituloProf = $model->titProf;
        $prof->save();
        $this->redirect(array('index'));
      }
      else
        $this->render('profesorForm',array(
          'form'=>$form,
      ));
    }

Se debe crear un modelo que indique cada uno de los campos que serán
manipulados por el formulario. Este archivo se llamará
*actualizarProfesorForm.php* y se ubicará en el directorio
*/protected/models*. El código de dicho archivo es que se mustra a
continuación:

::

    <?php
    class ActualizarProfesorForm extends CFormModel {
     public $idProf;
     public $nombProf;
     public $titProf;
     public function rules() {
       return array(
         array('idProf','required'),
         array('nombProf','required'),
         array('titProf','required'),
       );
     }
    }

Como indica el código anterior, es necesario crear la especificación del
formulario de profesor. Este archivo se llamará *profesorActualizar.php*
y se ubicará en el directorio *protected/views/profesor*. El archivo
define los diferentes campos que utilizará el formulario, como se
muestra a continuación:

::

    <?php
     return array(
       'title'=>'Datos de profesor',
       'elements'=>array(
         'nombProf'=>array(
           'type'=>'text',
           'maxlength'=>40,
         ),
         'idProf'=>array(
           'type'=>'text',
           'maxlength'=>40,
         ),
         'titProf'=>array(
           'type'=>'text',
           'maxlength'=>40,
         ),
       ),
       'buttons'=>array(
         'guardar'=>array(
           'type'=>'submit',
           'label'=>'Guardar',
         ),
       ),
     );

Ahora es necesario crear un pequeño archivo de vista que cuenta con
únicamente una instrucción. Este archivo se llamará *profesorForm.php*,
se ubicará bajo */protected/views/profesor* y su contenido es
simplemente el siguiente:

::

    <?php echo $form; ?>

Si se accede al enlace http://localhost/universidad/?r=profesor se podrá
observar cómo se muestra el nuevo listado de todos los profesores con la
opción de agregar datos.

Actualización de datos
----------------------

Para actualizar datos se puede reutilizar el formulario mostrado en la
sección anterior. En este caso se debe volver a modificar el archivo de
la vista del listado de profesores, llamada *profesorListado.php* y que
se ubica en el directorio *protected/views/profesor/*. La nueva versión
del archivo sería la siguiente:

::

    <h2>Listado de profesores</h2><br/>
    <a href="?r=profesor/agregar"/>
     <input type="button" value="Agregar"/></a>
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
       </td>
       </tr>
     <?php } ?>
    </table>

Luego se debe agregar la acción de actualizar al controlador de
profesor. El código de dicha acción es el que se muestra a continuación:

::

    public function actionActualizar($id) {
      $prof=Profesor::model()->find('idProfesor=:idProfesor', 
                                  array(':idProfesor'=>$id));
      $model = new ActualizarProfesorForm;
      $model->nombProf = $prof->nomProf;
      $model->idProf = $prof->idProfesor;
      $model->titProf = $prof->tituloProf;
      $form = new CForm('application.views.profesor.profesorActualizar',$model);
      if ($form->submitted('guardar')&& $form->validate()) {
        $prof->nomProf = $model->nombProf;
        $prof->idProfesor = $model->idProf;
        $prof->tituloProf = $model->titProf;
        $prof->save();
        $this->redirect(array('consulta'."&id=".$id));
      }
      else
        $this->render('profesorForm',array(
          'form'=>$form,
      ));
    }

Borrado de datos
----------------

La acción de borrado es la más sencilla de todas. Sin embargo, para
habilitarla se debe volver a modificar la vista del listado de
profesores. La versión final de este archivo sería la siguiente:

::

    <h2>Listado de profesores</h2><br/>
    <a href="?r=profesor/agregar"/>
     <input type="button" value="Agregar"/></a>
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

La acción de eliminar, que se debe agregar al controlador de profesores,
es bastante sencilla y se muestra a continuación:

::

    public function actionEliminar($id) {
      $prof=Profesor::model()->find('idProfesor=:idProfesor', 
                                  array(':idProfesor'=>$id));
      $prof->delete();
      $profs=Profesor::model()->findAll();
      $this->render('profesorListado',array(
          'profs'=>$profs,
       ));
    }

Si se accede al enlace http://localhost/universidad/?r=profesor se podrá
observar cómo se muestra el nuevo listado de todos los profesores con la
opción de agregar datos, modificar y borrar.
