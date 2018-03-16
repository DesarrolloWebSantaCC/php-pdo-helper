# Descripción

Clase utilitaria para manejar bases de datos MySQL. Compatible con PHP 5 y PHP 7. Utiliza la librería nativa PDO de PHP.

# Instalación

Copia el archivo `db.php` en el directorio donde quieras utilizar la clase, e inclúyela en tu código:

`include_once "PDOHelper.php";`

Crea una nueva instancia de la clase (es decir, una nueva conexión con la base de datos):

`$pdb = new PDOHelper($dbs, $usr, $pwd);`

#### Parámetros:
> * `$dbs`: Nombre de la base de datos.
> * `$usr`: Nombre de usuario.
> * `$pwd`: Contraseña.
> 
> _Devuelve:_ Identificador del objeto PDO instanciado.

Ya puedes empezar a utilizar las funciones de la clase.

# Modo de empleo

La clase incluye una serie de funciones para facilitar operaciones básicas con filas y tablas:

## `PDOHelper->checkTable( $table, $table_parameters )`

Comprueba si existe la tabla y, si no existe, la crea.

#### Parámetros:
> * `$table`: Nombre de la tabla.
> * `$table_parameters`: Parámetros de creación de la tabla.
> 
> _Devuelve:_ `TRUE` si la tabla existe o ha podido crearse, `FALSE` si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
if ($pdb->checkTable('mitabla', 'id int, nombre varchar(255), apellidos varchar(255)')) {
    echo 'La tabla existía o ha sido creada con los parámetros especificados.';
} else {
    echo 'La tabla no existe y no ha podido ser creada.';
}
```

## `PDOHelper->executeDB( $query )`

Ejecuta una consulta de MySQL. Esta función no devuelve resultados de consultas y se incluye solo para completar el repertorio de funciones. 

#### Parámetros:
> * `$query`: Consulta SQL.
> 
> _Devuelve:_ `TRUE` si la ejecución ha funcionado, `FALSE` si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
if ($pdb->executeDB( 'UPDATE mitabla SET VALUES nombre="José" WHERE nombre="Juan";' )) {
    echo 'Instrucción ejecutada con éxito.';
} else {
    echo 'Instrucción fallida.';
}
```
## `PDOHelper->get( $tabla, $valor, $columna, $busqueda ) `

Recupera un dato único de la fila que coincide con la búsqueda indicada.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la consulta.
> * `$valor`: Columna de la que recuperar el valor.
> * `$columna`: Columna cuyo valor hay que comparar.
> * `$busqueda`: Valor con el que comparar el valor de $columna.
> 
> _Devuelve:_ Valor solicitado si funciona, `FALSE` si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$apellidosDeJose = $pdb->get( 'mitabla', 'apellidos', 'nombre', 'José' );
if ($apellidosDeJose === FALSE) {
    echo 'No se ha encontrado ninguna fila en la que el valor de la columna nombre sea "José"';
} else {
    echo 'Apellidos de José: '.$apellidosDeJose;
}
```
## `PDOHelper->getArray( $tabla, [$columna [, $busqueda]] ) `

Recupera un dato único de la fila que coincide con la búsqueda indicada.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la consulta.
> * `$columna`: Columna cuyo valor hay que comparar. _(defecto = "")_
> * `$busqueda`: Valor con el que comparar el valor de $columna. _(defecto = "")_
> 
> _Devuelve:_ Matriz de filas solicitadas si funciona, FALSE si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$filas = $pdb->getArray( 'mitabla', 'nombre', 'José' );
if ($filas === FALSE) {
    echo 'La consulta ha fallado.';
} else {
    if (count($filas) === 0) {
        echo 'No se ha encontrado ninguna entrada con nombre = "José"';
    } else {
        echo 'Apellidos de entradas con nombre = "José":';
        foreach ($filas as $fila) {
            echo $fila['apellidos'];
        }
    }
}
```
## `PDOHelper->getSelectedArray( $tabla, [$condiciones [, $orden[, $direccion[, $limit]]]] )`

Recupera la matriz de filas que cumplen la condición de tener el valor proporcionado en una columna determinada.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la consulta.
> * `$condiciones`: Matriz de pares de clave y valor que estipulan condiciones de identidad para la búsqueda. _(defecto = array())_
> * `$orden`: Columna que ha de regir el orden de la matriz solicitada. _(defecto = "")_
> * `$direccion`: Dirección del orden solicitado. _(defecto = "ASC")_
> * `$limit`: Límite de número de filas recuperadas. _(defecto = "")_
> 
> _Devuelve:_ Matriz de filas solicitadas si funciona, FALSE si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$filas = $pdb->getSelectedArray( 'mitabla', array('nombre' => 'José'), 'DESC', '5' );
if ($filas === FALSE) {
    echo 'La consulta ha fallado.';
} else {
    if (count($filas) === 0) {
        echo 'No se ha encontrado ninguna entrada con nombre = "José"';
    } else {
        echo 'Entradas con nombre = "José", por orden descendiente, máximo 5:';
        foreach ($filas as $fila) {
            echo $fila['apellidos'];
        }
    }
}
```
## `PDOHelper->getRow( $tabla, $id )`

Recupera una fila de una tabla.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la consulta.
> * `$id`: ID de la fila buscada.
> 
> _Devuelve:_ Fila solicitada si funciona, FALSE en caso de fallo.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$fila = $pdb->getRow( 'mitabla', 1 );
if ($fila === FALSE) {
    echo 'La consulta ha fallado.';
} else {
    echo 'Apellidos de fila 1: '.$fila['apellidos'];
}
```
## `PDOHelper->insert( $tabla, $datos )`

Inserta una fila con datos proporcionados por un array asociativo en las columnas con nombre indicado por las claves del array.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la inserción.
> * `$datos`: Array asociativo cuyas claves coinciden con las columnas en las que insertar los valores.
> 
> _Devuelve:_ ID de la fila insertada, FALSE en caso de fallo.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$id = $pdb->insert( 'mitabla', array('nombre' => 'Juan', 'apellidos' => 'García') );
if ($id === FALSE) {
    echo 'No se ha podido insertar la entrada.';
} else {
    echo 'ID de la fila insertada: '.$id;
}
```
## `PDOHelper->insertEx( $tabla, $datos, [$op [, $values]] )`

Inserta una fila con datos proporcionados por un array asociativo en las columnas con nombre indicado por las claves del array y aplica a esos datos la operación indicada.

#### Parámetros:
> * `$tabla`: Tabla en la que hacer la inserción.
> * `$datos`: Array asociativo cuyas claves coinciden con las columnas en las que insertar los valores.
> * `$op`: Operación que debe incluirse para cada dato suministrado.
> * `$values`: Array de valores a los que debe aplicarse la operación.
> 
> _Devuelve:_ ID de la fila insertada, FALSE en caso de fallo.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
// Inserta la entrada con la primera letra del apellido en mayúscula
$id = $pdb->insertEx( 'mitabla', array('nombre' => 'Juan', 'apellidos' => 'garcía'), 'ucfirst', array('apellidos') );
if ($id === FALSE) {
    echo 'No se ha podido insertar la entrada.';
} else {
    echo 'ID de la fila insertada: '.$id;
}
```
## `PDOHelper->update( $tabla, $datos, $id )`

Cambia los datos de la fila indicada de una tabla según los datos proporcionados por un array asociativo en las columnas con nombre indicado por las claves del array.

#### Parámetros:
> * `$tabla`: Tabla que se va a modificar.
> * `$datos`: Datos y valores que se van a modificar.
> * `$id`: ID de la fila que se va a modificar.
> 
> _Devuelve:_ TRUE si funciona, FALSE si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$res = $pdb->update( 'mitabla', array('nombre' => 'Jorge'), 1 );
if ($res === FALSE) {
    echo 'No se ha podido modificar la entrada.';
} else {
    echo 'Fila modificada.';
}
```
## `PDOHelper->delete( $tabla, $datos )`

Elimina la fila indicada de una tabla según los datos proporcionados por un array asociativo en las columnas con nombre indicado por las claves del array.

#### Parámetros:
> * `$tabla`: Tabla que se va a modificar.
> * `$datos`: Datos que se van a comparar para seleccionar las filas que se quieren eliminar.
> 
> _Devuelve:_ TRUE si funciona, FALSE si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$res = $pdb->delete( 'mitabla', array('nombre' => 'Juan') );
if ($res === FALSE) {
    echo 'No han podido eliminarse las entradas con nombre = "Juan"';
} else {
    echo 'Se han eliminado las entradas con nombre = "Juan".';
}
```
## `PDOHelper->query( $sql )`

Hacer consulta SQL.

#### Parámetros:
> * `$sql`: Consulta SQL.
> 
> _Devuelve:_ Matriz de resultados si funciona, FALSE si no.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$filas = $pdb->query( 'SELECT * FROM mitabla WHERE nombre="Juan"' );
if ($filas === FALSE) {
    echo 'No han podido recuperarse los datos de la consulta.';
} else {
    echo 'Apellidos de entradas con nombre = "Juan":';
    foreach ($filas as $fila) {
        echo $fila['apellidos'];
    }
}
```
## `PDOHelper->id()`

Recupera el identificador del objeto (la conexión con la base de datos MySQL).

#### Parámetros:
> * `$sql`: Consulta SQL.
> 
> _Devuelve:_ Identificador del objeto (la conexión con la base de datos MySQL).

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$pdb_id = $pdb->id();
if ($pdb === $pdb_id) {
    echo 'Se ha recuperado el identificador del objeto PDO.';
} else {
    echo 'Algo ha fallado';
}
```
## `PDOHelper->msg`

Último mensaje de error.

> _Devuelve:_ Último mensaje de error generado por operaciones fallidas.

#### Ejemplo:

```php
$pdb = new PDOHelper('mibasededatos', 'usuario', 'contraseña');
$res = $pdb->delete( 'tablainexistente', array('nombre' => 'Juan') );
if ($res === FALSE) {
    echo 'No han podido eliminarse las entradas con nombre = "Juan"';
    echo 'Mensaje de error:';
    echo $pdb->msg;
} else {
    echo 'Se han eliminado las entradas con nombre = "Juan".';
}
```
