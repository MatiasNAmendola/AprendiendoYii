Búsquedas y formularios en Yii
==============================

El framework Yii cuenta con una serie de clases que facilitan la
programación de formularios. Esto permite que el desarrollador se
desentienda de la creación del código html para mostrar cada campo, como
de la validación de los datos.

Estas características hacen de Yii un magnífico framework para el
desarrollo y programación rápida de aplicaciones. El siguiente tutorial
muestra un ejemplo muy sencillo del uso de formularios para realizar la
búsqueda de datos.

Creando el modelo del formulario
--------------------------------

En Yii antes de crear un formulario, se debe crear un modelo de datos
que es independiente de cualquier tabla de datos. El modelo del
formulario especifica los nombres de los campos a incluir en formulario,
sus tipos y reglas de validación.

El modelo del formulario, llamado *buscarEscuelaForm.php* reside en el
mismo directorio que los modelos de tablas de datos, y que es el
directorio */universidad/protected/models/*. El código de dicho archivo
es el siguiente:

::

    <?php
    class BuscarEscuelaForm extends CFormModel {
     public $nombre;
     public function rules() {
       return array(
         array('nombre','required'),
       );
     }
    }

Modificando el controlador
--------------------------

Una vez que se cuenta con el modelo, se puede proceder a utilizar dicho
modelo en el controlador. En este caso se modificará el controlador
llamado *escuelaController.php* que se encuentra en el directorio
*/universidad/protected/controllers/* y cuyo código quedará de la
siguiente forma:

::

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

Creación de las vistas
----------------------

Para que el formulario sea presentado en pantalla es necesario crear dos
vistas. La primera de ellas no es realmente una vista, sino que más bien
consiste en la declaración de los diferentes componentes del formulario.
Este archivo se llamará *escuelaBuscar.php* y se ubicará en el
directorio */universidad/protected/views/escuela*. Su código será el
siguiente:

::

    <?php
     return array(
       'title'=>'Buscar escuela',
       'elements'=>array(
         'nombre'=>array(
           'type'=>'text',
           'maxlength'=>40,
         ),
       ),
       'buttons'=>array(
         'buscar'=>array(
           'type'=>'submit',
           'label'=>'Buscar',
         ),
       ),
     );

El segundo archivo llamado *buscarForm.php*, y que se ubica en el mismo
directorio, conforma la vista real. Sin embargo, el código necesario de
dicho archivo es mínimo pues el framework se encarga de generar la
mayoría del html. El código de este archivo es simplemente el siguiente:

::

    <?php echo $form; ?>

Se puede probar esta nueva funcionalidad mediante el enlace
http://localhost/universidad/?r=escuela/buscar y digitando el nombre de
una alguna escuela (puede ser cualquier palabra del nombre).

Mejorando la apariencia de las tablas
-------------------------------------

Por último, se puede mejorar la apariencia de las diferentes tablas que
genera el sistema, adicionalmente nuevas especificaciones a la hoja de
estilo. Para ello se deben agregar las definiciones que se muestran a
continuación, en el archivo *main.css* que encuentra en el directorio
*/universidad/css*:

::

    table {
     text-align: center;
     font-family: Verdana, Geneva, Arial, Helvetica, sans-serif ;
     font-weight: normal;
     font-size: 11px;
     color: #fff;
     width: 100%;
     background-color: #666;
     border: 0px;
     border-collapse: collapse;
     border-spacing: 0px;
    }
    table td {
     background-color: #CCC;
     color: #000;
     padding: 4px;
     text-align: left;
     border: 1px #fff solid;
    }
    table td.hed {
     background-color: #666;
     color: #fff;
     padding: 4px;
     text-align: left;
     border-bottom: 2px #fff solid;
     font-size: 12px;
     font-weight: bold;
    } 

