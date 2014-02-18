Consulta de datos con Yii
=========================

El framework Yii cuenta con varios mecanismos para el acceso a bases de datos. Uno de estos mecanismos utiliza el concepto de DAOs (Data Access Objects) y permite una gran flexibilidad en la manipulación de datos en una base de datos. El problema de utilizar DAOs es que se debe escribir más código para realizar cualquier operación y los enunciados SQL normalmente quedan mezclados con las instrucciones de las operaciones.

El otro mecanismo utilizado por Yii para el acceso a datos, es el Registro Activo (AR). Utilizando este mecanismo resulta menos tedioso escribir rutinas de recuperación y actualización de datos, debido a que Yii abstrae muchos de los detalles de implementación internos relacionados con la ejecución de las consultas a la base de datos.

Continuando con la serie de tutoriales sobre Yii, a continuación se muestra cómo programar aplicaciones que accedan a bases de datos. Se continúa con el ejemplo iniciado en un tutorial anterior.

Creando y configurando la base de datos
---------------------------------------

Para empezar se debe crear la base de datos. Para crear y administrar bases de datos en *SQLite* existen muchas herramientas disponibles. Una de estas herramientas funciona como un plug-in de *Firefox* y es llamada *SQLite Manager* .

Utilizando el ejemplo del Sistema Universitario se podrían crear las tablas relativas a carreras y profesores. Dicho archivo se puede llamar *universidad.sqlite* y se ubicaría en el directorio *universidad/protected/data/*. El siguiente código SQL muestra la forma de crear las tablas:

    CREATE TABLE "escuela" ("idEscuela" INTEGER PRIMARY KEY  AUTOINCREMENT NOT NULL, 
        "nombEscuela" char(20), "facultad" char(20), "nomDirect" char(20));
    CREATE TABLE "profesor" ("idProfesor"  INTEGER PRIMARY KEY  AUTOINCREMENT  NOT NULL, 
        "cedProfesor" CHAR(10), "nomProf" VARCHAR(20), "tituloProf" CHAR(20),
        "idEscuela" INTEGER, FOREIGN KEY (idEscuela) REFERENCES escuela(idEscuela));

Para tener algunos datos de ejemplo que permitan ejecutar pruebas y observar la salida de las diferentes pantallas, se puede utilizar el siguiente conjunto de enunciados SQL:

    INSERT INTO "escuela" VALUES(1,'Escuela de Economía',
     'Ciencias Económicas','Juan Perez');
    INSERT INTO "escuela" VALUES(2,'Escuela de Enfermería',
     'Ciencias de la Salud','Cristina Suarez');
    INSERT INTO "profesor" VALUES(1,'102340567','Pedro Pereira','Licenciado',1);
    INSERT INTO "profesor" VALUES(2,'201230567','Ligia Lima','Maestría',2);
    INSERT INTO "profesor" VALUES(3,'209860765','Juan Gonzalez','Doctorado',1);
    INSERT INTO "profesor" VALUES(4,'803450567','Eugenia Trejos','Licenciado',2);

Al realizar la configuración de la base de datos se debe editar el archivo *main.php* que se encuentra ubicado en el directorio *universidad/protected/config/* e incluir la declaración de la conexión a la base de datos, tal como se muestra a continuación:

    <?php
    return array(
     'name'=>'Sistema Universitario',
     'defaultController'=>'site',
     'layout'=>'main',
     'components'=>array(
       'db'=>array(
         'class'=>'system.db.CDbConnection',
         'connectionString'=>
           'sqlite:'.dirname(__FILE__).'/../data/universidad.sqlite',
        ),
     ),
    );

Definición de clases mediante Registro Activo
---------------------------------------------

Para obtener acceso a los datos es necesario crear una clase por cada tabla. Sin embargo, Yii realiza mucho del trabajo para obtener los nombres y tipos de los diferentes campos de las tablas, lo que hace menos tedioso esta tarea de creación de clases.

La primera clase que se creará será la de profesor, el archivo que la contiene se llamará *profesor.php* y se ubicará en el directorio *universidad/protected/models/*. Su código es sumamente simple tal como se muestra a continuación:

    <?php
    class Profesor extends CActiveRecord {
     public static function model($className=__CLASS__) {
       return parent::model($className);
     }
    }

Para que esta clase sea incorporada a la aplicación, es necesario incluir una declaración en el archivo *main.php* que se encuentra ubicado en *universidad/protected/config/*. O sea, que luego de agregar la definición de la base de datos y la importación del modelo, el archivo quedaría de la siguiente forma:

    <?php
    return array(
     'name'=>'Sistema Universitario',
     'defaultController'=>'site',
     'layout'=>'main',
     'components'=>array(
       'db'=>array(
         'class'=>'system.db.CDbConnection',
         'connectionString'=>
           'sqlite:'.dirname(__FILE__).'/../data/universidad.sqlite',
        ),
     ),
     'import'=>array(
           'application.models.*',
     ),
    );

Accesando los datos
-------------------

Se puede asociar un controlador a cada tabla de forma que se encargue de todas las operaciones que se ejecutan sobre la misma. En este ejemplo únicamente se mostrará la forma de implementar las operaciones de listado de profesores, y de detalle de profesor. El archivo de este controlador se llamará *ProfesorController.php* y residirá en el directorio *universidad/protected/controllers/*. Su código sería el siguiente:

    <?php
    class ProfesorController extends CController {
     public function actionIndex() {
       $profs=Profesor::model()->findAll();
       $this->render('profesorListado',array(
           'profs'=>$profs,
        ));
     }
     public function actionConsulta($id) {
       $prof=Profesor::model()->find('idProfesor=:idProfesor', 
                                   array(':idProfesor'=>$id));
       $this->render('profesorDetalle',array(
           'prof'=>$prof,
        ));
     }
    }

El listado de todos los profesores se obtiene mediante la vista *profesorListado.php* que residirá en el directorio *universidad/protected/views/profesor/*. Dicha página itera a través de todos los registros y los presenta utilizando una tabla html. El código de dicho archivo es el siguiente:

    <h2>Listado de profesores</h2>
    <table border="1">
     <tr><th>Nombre</th><th>Título</th><th>Acción</th></tr>
     <?php foreach ($profs as $prof) { ?>
       <tr><td><?php echo $prof->nomProf ?></td>
       <td><?php echo $prof->tituloProf ?></td>
       <td><a href="?r=profesor/consulta&id=<?php
         echo $prof->idProfesor ?>"/>
         Detalle</a></td>
       </tr>
     <?php } ?>
    </table>

Si se accede al enlace [http://localhost/universidad/?r=profesor](http://localhost/universidad/?r=profesor) se podrá observar cómo se muestra el listado de todos los profesores.

Para poder desplegar el detalle del profesor se crea la vista *profesorDetalle.php* que residirá en el directorio *universidad/protected/views/profesor/*. Dicha vista simplemente presentará los datos mediante una tabla, tal como se muestra a continuación:

    <h2>Detalle de profesor</h2>
    <table>
     <tr><td><b>Ident.:</b></td>
       <td><?php echo $prof->cedProfesor ?></td></tr>
     <tr><td><b>Nombre:</b>
       </td><td><?php echo $prof->nomProf ?></td></tr>
     <tr><td><b>Título:</b></td>
       <td><?php echo $prof->tituloProf ?></td></tr>
    </table>

Si se accede al enlace [http://localhost/universidad/?r=profesor/consulta&id=1](http://localhost/universidad/?r=profesor/consulta&id=1) se podrá observer la página de detalle de profesor.

Acceso a datos relacionados
---------------------------

Ya que las diferentes tablas de datos pueden estar relacionadas, es necesario especificar dichas relaciones en la clase del registro activo. Esto se realiza mediante la función relations que devuelve un arreglo en el que se definen todas las relaciones de la tabla en cuestión.

El siguiente código muestra el contenido del archivo escuela.php que reside en el directorio *universidad/protected/models/* y que especifica el registro activo de la tabla de escuelas:

    <?php
    class Escuela extends CActiveRecord {
     public static function model($className=__CLASS__) {
       return parent::model($className);
     }
     public function relations() {
       return array(
         'escuela2profesor'=>array(self::HAS_MANY,'Profesor','idEscuela'),
       );
     }
    }

Ahora se puede crear un controlador que permita procesar las operaciones asociadas a la tabla de escuela. En este caso el archivo se llamará *EscuelaController.php* y residirá en el directorio *universidad/protected/controllers/*. Su código sería el que se muestra a continuación:

    <?php
    class EscuelaController extends CController {
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
    }

El listado de todas las escuelas se obtiene mediante la vista escuelaListado.php que residirá en el directorio *universidad/protected/views/escuela/*. Dicha página itera a través de todos los registros y los presenta utilizando una tabla html. El código de dicho archivo es el siguiente:

    <h2>Listado de escuelas</h2>
    <table border="1">
     <tr><th>Nombre</th><th>Facultad</th><th>Acción</th></tr>
     <?php foreach ($escuelas as $escuela) { ?>
       <tr><td><?php echo $escuela->nombEscuela ?></td>
       <td><?php echo $escuela->facultad ?></td>
       <td><a href="?r=escuela/consulta&id=<?php echo
         $escuela->idEscuela ?>"/>
         Detalle</a></td>
       </tr>
     <?php } ?>
    </table>

Si se accede al enlace [http://localhost/universidad/?r=escuela](http://localhost/universidad/?r=escuela) se podrá observar la página con el listado de escuelas.

Ahora para poder desplegar los detalles de la escuela se crea la vista *escuelaDetalle.php* que residirá en el directorio *universidad/protected/views/escuela/*. Dicha vista simplemente presentará los datos mediante una tabla, tal como se muestra a continuación:

    <h2>Detalle de escuela</h2>
    <table>
     <tr><td><b>Nombre:</b></td>
       <td><?php echo $escuela->nombEscuela ?></td></tr>
     <tr><td><b>Facultad:</b></td>
       <td><?php echo $escuela->facultad ?></td></tr>
     <tr><td><b>Director:</b></td>
       <td><?php echo $escuela->nomDirect ?></td></tr>
    </table>
    <h3>Profesores asociados</h3>
    <table border="1">
     <tr><th>Nombre</th><th>Título</th><th>Acción</th></tr>
     <?php foreach ($profs as $prof) { ?>
       <tr><td><?php echo $prof->nomProf ?></td>
       <td><?php echo $prof->tituloProf ?></td>
       <td><a href="?r=profesor/consulta&id=<?php
         echo $prof->idProfesor ?>"/>
         Detalle</a></td>
       </tr>
     <?php } ?>
    </table>

Si se accede al enlace [http://localhost/universidad/?r=escuela/consulta&id=1](http://localhost/universidad/?r=escuela/consulta&id=1) se puede observar la página de detalle de escuela junto con sus profesores asociados.
