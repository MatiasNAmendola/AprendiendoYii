Widgets de Yii
==============

Los *widgets* son componentes que se utilizan un Yii especialmente para propósitos de presentación. Un *widget* es generalmente incrustrado en una vista para generar una interfaz de usuario más compleja, aunque auto contenida. Por ejemplo, un *widget* de calendario puede se utilizado para seleccionar una fecha en un formulario.

Este tutorial realiza algunos cambios en la interfaz incorporando una serie de *widgets*. Igual que antes, se utilizará el ejemplo del Sistema Universitario de tutoriales anteriores.

Requisito previo
----------------

Antes de empezar a utilizar los widgets de Yii es necesario crear un directorio llamado *assets* bajo el directorio principal en donde se ejecuta la aplicación. En este caso el grupo de directorios del primer nivel de la jerarquía luciría de la siguiente forma:

    universidad
      assets
      css
      protected

Manejando la paginación
-----------------------

La paginación puede ser muy fácilmente manejada utilizando el widget *CGridView*. Para ello basta con reemplazar todo el código anterior que construía la tabla html con la invocación a este widget. Para el caso del listado de profesor se modifica el archivo *profesorListado.php* que se encuentra en el directorio *protected/views/profesor* por (únicamente) el siguiente código:

    <h2>Listado de profesores</h2><br/>
    <a href="?r=profesor/create"/>
    <input type="button" value="Agregar"/></a>
    <?php
     $this->widget('zii.widgets.grid.CGridView', array(
      'dataProvider'=>$profs,
     ));

La función *actionIndex* del controlador de profesor también debe ser modificada por unas pocas de código. De dicha forma, esta función ubicada en el archivo *ProfesorController.php* que a su vez se encuentra en el directorio */protected/controllers*, queda de la siguiente forma:

    public function actionIndex($start=0,$block=5) {
      $profs=new CActiveDataProvider('Profesor',array(
        'pagination'=>array(
          'pageSize'=>4,
         ),
      ));
      $this->render('profesorListado',array(
          'profs'=>$profs,
       ));
    }

La nueva interfaz puede ser observada al visitar la dirección [http://localhost/universidad/?r=profesor](http://localhost/universidad/?r=profesor)

Mejorando el despliegue de columnas
-----------------------------------

Como se puede observar en la nueva pantalla presentada, los nombres de las columnas son tomadas directamente del modelo de datos. Para modificar estos nombres por otros más adecuados se pueden modificar los parámetros del widget. Ahora, el código del archivo *profesorListado.php* quedaría de la siguiente forma:

    <h2>Listado de profesores</h2><br/>
    <a href="?r=profesor/create"/>
    <input type="button" value="Agregar"/></a>
    <?php
     $this->widget('zii.widgets.grid.CGridView', array(
      'dataProvider'=>$profs,
      'columns'=>array(
        'idProfesor:number:Id',
        'cedProfesor:text:Cédula',
        'nomProf:text:Nombre',
        'tituloProf:text:Título',
        array('class'=>'CButtonColumn','header'=>'Operaciones'
        ),
      ),
     ));

Debido a que se crea una nueva columna que presenta las operaciones que se pueden realizar sobre los registros, es necesario cambiar los nombres de las funciones, en el controlador, de forma que coincidan con los que genera el *widget*. De esta forma, el archivo *ProfesorController.php* quería de la siguiente manera:

    class ProfesorController extends CController {
    public function actionIndex() {
     $profs=new CActiveDataProvider('Profesor',array(
       'pagination'=>array(
         'pageSize'=>4,
        ),
     ));
     $this->render('profesorListado',array(
         'profs'=>$profs,
      ));
    }
    public function actionView($id) {
      $prof=Profesor::model()->find('idProfesor=:idProfesor',
                                  array(':idProfesor'=>$id));
      $this->render('profesorDetalle',array(
          'prof'=>$prof,
       ));
    }
    public function actionCreate() {
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
    public function actionUpdate($id) {
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
    public function actionDelete($id) {
     $prof=Profesor::model()->find('idProfesor=:idProfesor',
                                 array(':idProfesor'=>$id));
     $prof->delete();
     $profs=Profesor::model()->findAll();
     $this->render('profesorListado',array(
         'profs'=>$profs,
      ));
    }
    }

Manejando los formularios
-------------------------

En los formularios, que son generados automáticamente, se le puede agregar una etiqueta más significativa a cada campo. Además, se puede agregar una descripción inicial. Para el caso del formulario del profesor, que utiliza el archivo *profesorActualizar.php* ubicado en el directorio */protected/views/profesor*, estas modificaciones quedarían de la siguiente forma:

    <?php
    $data = CHtml::listData(Escuela::model()->findAll(),
                           'idEscuela', 'nombEscuela');
    return array(
      'title'=>'Datos de profesor',
      'description'=>'Ingrese la siguiente información:',
      'elements'=>array(
        'nombProf'=>array(
          'type'=>'text',
          'maxlength'=>40,
          'label'=>'Nombre'
        ),
        'idProf'=>array(
          'type'=>'text',
          'maxlength'=>40,
          'label'=>'Ident.'
        ),
        'titProf'=>array(
          'type'=>'text',
          'maxlength'=>40,
          'label'=>'Título'
        ),
        'idEscuela'=>array(
          'type'=>'dropdownlist',
          'items'=>$data,
          'label'=>'Escuela'
        ),
      ),
      'buttons'=>array(
        'guardar'=>array(
          'type'=>'submit',
          'label'=>'Guardar',
        ),
      ),
    );
