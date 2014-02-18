Enlazando entidades en Yii
==========================

En el anterior tutorial se mostró cómo agregar y actualizar nuevos
registros en Yii. El problema que presenta dicho tutorial es que aún
cuando se crea el nuevo registro de Profesor este no queda enlazado con
la Escuela adecuada. De hecho, en la pantalla de detalle de Profesor no
aparece la información sobre la Escuela asociada.

En este tutorial se mostrarán algunos cambios al ejemplo que se ha ido
desarrollando de forma que se pueda realizar el enlace entre los
registros y se presente la información relacionada.

El primer archivo a modificar es el asociado al detalle del profesor,
llamado *profesorDetalle.php* y que se encuentra en el directorio
*/protected/views/profesor/profesorDetalle.php*:

::

    <?php
     $escuela=Escuela::model()->find('idEscuela = :idEscuela', 
                                   array(':idEscuela'=>$prof->idEscuela)); ?>
    <h2>Detalle de profesor</h2><br/>
    <table>
     <tr><td><b>Ident.:</b></td>
       <td><?php echo $prof->idProfesor ?></td></tr>
     <tr><td><b>Nombre:</b>
       </td><td><?php echo $prof->nomProf ?></td></tr>
     <tr><td><b>Título:</b></td>
       <td><?php echo $prof->tituloProf ?></td></tr>
     <tr><td><b>Escuela:</b></td>
       <td><?php echo $escuela->nombEscuela ?></td></tr>
    </table>

Como se puede observar se ha incorporado una instrucción para recuperar
el nombre de la escuela utilizando para ello el campo *idEscuela* del
registro del *Profesor*. Luego se utiliza el campo de *nombEscuela* del
modelo de *Escuela*.

Ahora se realizar los cambios en el archivo *actualizarProfesorForm.php*
que se encuentra en el directorio */protected/models/*

::

    <?php
    class ActualizarProfesorForm extends CFormModel {
     public $idProf;
     public $nombProf;
     public $titProf;
     public $idEscuela;
     public function rules() {
       return array(
         array('idProf','required'),
         array('nombProf','required'),
         array('titProf','required'),
         array('idEscuela','required'),
       );
     }
    }

También, se deben modificar el archivo de *profesorActualizar.php* que
se encuentra en el directorio */protected/views/profesor/*:

::

    <?php
     $data = CHtml::listData(Escuela::model()->findAll(),
                             'idEscuela', 'nombEscuela');
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
         'idEscuela'=>array(
           'type'=>'dropdownlist',
           'items'=>$data,
         ),
       ),
       'buttons'=>array(
         'guardar'=>array(
           'type'=>'submit',
           'label'=>'Guardar',
         ),
       ),
     );

Como se puede ver en este código, se utiliza la misma estrategia
anterior para acceder a los datos del modelo de Escuela. Por último, se
debe modificar el controlador de *Profesor* para que refleje estos
nuevos cambios. Dicho controlador consiste del archivo
*ProfesorController.php* que se encuentra en el directorio
*/protected/controllers/*. Sin embargo, únicamente se requiere modificar
dos métodos de dicho archivo:

::

    public function actionAgregar() {
      $prof = new Profesor;
      $model = new ActualizarProfesorForm;
      $form = 
        new CForm('application.views.profesor.profesorActualizar',$model);
      if ($form->submitted('guardar')&& $form->validate()) {
        $prof->nomProf = $model->nombProf;
        $prof->idProfesor = $model->idProf;
        $prof->tituloProf = $model->titProf;
        $prof->tituloProf = $model->idEscuela;
        $prof->save();
        $this->redirect(array('index'));
      }
      else
        $this->render('profesorForm',array(
          'form'=>$form,
      ));
    }
    public function actionActualizar($id) {
      $prof=Profesor::model()->find('idProfesor=:idProfesor', 
                                  array(':idProfesor'=>$id));
      $model = new ActualizarProfesorForm;
      $model->nombProf = $prof->nomProf;
      $model->idProf = $prof->idProfesor;
      $model->titProf = $prof->tituloProf;
      $model->idEscuela = $prof->idEscuela;
      $form = 
        new CForm('application.views.profesor.profesorActualizar',$model);
      if ($form->submitted('guardar')&& $form->validate()) {
        $prof->nomProf = $model->nombProf;
        $prof->idProfesor = $model->idProf;
        $prof->tituloProf = $model->titProf;
        $prof->idEscuela = $model->idEscuela;
        $prof->save();
        $this->redirect(array('consulta'."&id=".$id));
      }
      else
        $this->render('profesorForm',array(
          'form'=>$form,
      ));
    }

El nuevo dato, con la información de la escuela, se puede observar al
visitar el enlace http://localhost/universidad/?r=profesor/consulta&id=1
