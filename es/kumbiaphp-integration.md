# Integración con KumbiaPHP

## Instalación con Composer

Para utilizar el ActiveRecord en KumbiaPHP, asegúrate de tener [KumbiaPHP](https://kumbiaphp.com/blog/) en su versión
0.9RC o superior. Aquí tienes una guía paso a paso para instalarlo usando [Composer](https://getcomposer.org).

1. **Crear el archivo `composer.json` en la raíz del proyecto si no existe:**

   La estructura de tu proyecto debería verse algo así:

   ```plaintext
   --proyecto  
       |  
       |--vendor  
       |--default  
       |--core  
       |--composer.json        // Aquí va nuestro archivo
   ```

2. **Añadir el siguiente código en `composer.json`:**

   ```json
   {
       "require": {
           "kumbia/activerecord": "dev-master"
       }
   }
   ```

3. **Ejecutar el comando `composer install`:**

   Abre una terminal en la raíz de tu proyecto y ejecuta:

   ```sh
   composer install
   ```

4. **Usar el autoload de composer**

   Como se utilizó Composer para instalar el ActiveRecord, entonces debemos cargar el autoload de Composer dentro de la
   aplicación, para ello edita el archivo `index.php` de la carpeta `public` y antes de cargar el bootstrap de KumbiaPHP
   pega las siguientes líneas:

   ```php
   require_once '../../vendor/autoload.php';
   ```

5. **Configurar la base de datos:**

   Abre el archivo de configuración de base de datos

   ```plaintext
   vendor/kumbia/activerecord/config/config_databases.php
   ```
   Copia la cadena de conexión llamada `default`, es similar a la siguiente:

   ```sh
   'default' => [
       'dsn'      => 'mysql:host=localhost;dbname=mi_base_de_datos',
       'username' => 'mi_usuario',
       'password' => 'mi_contraseña',
   ],
   ```
   Luego, pégala en el archivo `app/config/databases.php` y modifícala con las credenciales de tu base de datos.

6. **(Opcional) Añadir archivos en `app/libs/`:**

   Puedes optar por añadir `lite_record.php` y/o `act_record.php` para utilizar `LiteRecord` y `ActiveRecord` respectivamente.

### LiteRecord

Para aquellos que prefieren usar SQL directamente pero también quieren las ventajas de un mini-ORM, aquí tienes cómo configurar `LiteRecord`:

- **Crear el archivo `lite_record.php` en `app/libs/`:**

   ```php
   <?php
   // app/libs/lite_record.php

   /**
    * Record 
    * Para los que prefieren SQL con las ventajas de un ORM
    *
    * Clase padre para añadir tus métodos
    *
    * @category Kumbia
    * @package ActiveRecord
    * @subpackage LiteRecord
    */

   // use Kumbia\ActiveRecord\LiteRecord as ORM;

   class LiteRecord extends \Kumbia\ActiveRecord\LiteRecord
   {

   }
   ```

### ActRecord

Para utilizar el ActiveRecord completo, sigue estos pasos:

- **Crear el archivo `act_record.php` en `app/libs/`:**

   ```php
   <?php
   // app/libs/act_record.php

   /**
    * ActiveRecord Nuevo
    *
    * Clase padre para añadir tus métodos
    *
    * @category Kumbia
    * @package ActiveRecord
    * @subpackage ActiveRecord
    */

   // use Kumbia\ActiveRecord\ActiveRecord;

   class ActRecord extends \Kumbia\ActiveRecord\ActiveRecord
   {

   }
   ```

¡Y eso es todo! Ahora tienes ActiveRecord configurado y listo para usar en tu proyecto KumbiaPHP. 🚀

## Integración con el sistema de vistas

Integrar ActiveRecord con el sistema de vistas de KumbiaPHP te permite manejar tus modelos y vistas de manera eficiente,
simplificando el desarrollo de aplicaciones web. Aquí te proporciono un ejemplo completo para demostrar cómo se puede
lograr esta integración.

### Creación del Modelo

Vamos a crear un modelo para manejar los productos en nuestra base de datos.

- Crea el archivo `producto.php` en `app/models/`.

```php
<?php
// app/models/producto.php

class Producto extends LiteRecord
{
    protected static $table = 'productos';

    protected function _beforeSave()
    {
        // Garantiza que el campo 'precio' en esta tabla sea siempre mayor que cero cuando se guarda una instancia de
        // esta clase (un registro) en la tabla 'productos'.
        return $this->precio > 0;
    }
}
```

### Creación del Controlador

El controlador manejará las operaciones CRUD y pasará los datos a las vistas.

- Crea el archivo `producto_controller.php` en `app/controllers/`.

```php
<?php
// app/controllers/producto_controller.php

class ProductoController extends AppController
{
    public function index()
    {
        $this->productos = Producto::all();
    }

    public function ver($id)
    {
        $this->producto = Producto::get($id);
        if (!$this->producto) {
            Flash::error('Producto no encontrado');
            return Redirect::to();
        }
    }

    public function crear()
    {
        if (Input::hasPost('producto')) {
            $producto = new Producto(Input::post('producto'));
            if ($producto->create()) {
                Flash::valid('Producto creado con éxito');
                return Redirect::to();
            }
            
            Flash::error('Error al crear el producto');
        }
    }

    public function editar($id)
    {
        $producto = Producto::get($id);
        if (!$producto) {
            Flash::error('Producto no encontrado');
            return Redirect::to();
        }

        if (Input::hasPost('producto')) {
            if ($producto->update(Input::post('producto'))) {
                Flash::valid('Producto actualizado con éxito');
                return Redirect::to();
            }
            
            Flash::error('Error al actualizar el producto');
        }

        $this->producto = $producto;
    }

    public function eliminar($id)
    {
        if (Producto::delete($id)) {
            Flash::valid('Producto eliminado con éxito');
        } else {
            Flash::error('Error al eliminar el producto');
        }
        return Redirect::to();
    }
}

```

### Creación de las Vistas

Vamos a crear las vistas para manejar las operaciones CRUD.

1. **Vista Index**

   Crea el archivo `index.phtml` en `app/views/producto/`.

```php
<?php
// app/views/producto/index.phtml

echo Html::link('producto/crear', 'Crear nuevo producto', 'class="btn btn-primary"');
?>

<?php View::content(); ?>

<table>
    <thead>
    <tr>
        <th>ID</th>
        <th>Nombre</th>
        <th>Precio</th>
        <th>Acciones</th>
    </tr>
    </thead>
    <tbody>
    <?php foreach ($productos as $producto): ?>
        <tr>
            <td><?= $producto->id ?></td>
            <td><?= $producto->nombre ?></td>
            <td><?= $producto->precio ?></td>
            <td>
                <?= Html::link("producto/ver/{$producto->id}", 'Ver', 'class="btn btn-info"') ?>
                <?= Html::link("producto/editar/{$producto->id}", 'Editar', 'class="btn btn-warning"') ?>
                <?= Html::link("producto/eliminar/{$producto->id}", 'Eliminar', 'class="btn btn-danger"') ?>
            </td>
        </tr>
    <?php endforeach; ?>
    </tbody>
</table>

```

2. **Vista Ver**

   Crea el archivo `ver.phtml` en `app/views/producto/`.

```php
<?php
// app/views/producto/ver.phtml
?>

    <h1>Ver Producto</h1>
    <p><strong>ID:</strong> <?= $producto->id ?></p>
    <p><strong>Nombre:</strong> <?= $producto->nombre ?></p>
    <p><strong>Precio:</strong> <?= $producto->precio ?></p>

<?= Html::link('producto/index', 'Volver', 'class="btn btn-primary"') ?>

```

3. **Vista Crear**

   Crea el archivo `crear.phtml` en `app/views/producto/`.

```php
<?php
// app/views/producto/crear.phtml
?>

<h1>Crear Producto</h1>
<?php View::content(); ?>
<?= Form::open() ?>
<div>
    <?= Form::label('Nombre', 'producto_nombre') ?>
    <?= Form::text('producto.nombre') ?>
</div>
<div>
    <?= Form::label('Precio', 'producto_precio') ?>
    <?= Form::text('producto.precio') ?>
</div>
<?= Form::submit('Crear', 'class="btn btn-primary"') ?>
<?= Form::close() ?>

<?= Html::link('producto/index', 'Volver', 'class="btn btn-default"') ?>

```

4. **Vista Editar**

   Crea el archivo `editar.phtml` en `app/views/producto/`.

```php
<?php
// app/views/producto/editar.phtml
?>

<h1>Editar Producto</h1>
<?php View::content(); ?>
<?= Form::open() ?>
<div>
    <?= Form::label('Nombre', 'producto_nombre') ?>
    <?= Form::text('producto.nombre') ?>
</div>
<div>
    <?= Form::label('Precio', 'producto_precio') ?>
    <?= Form::text('producto.precio') ?>
</div>
<?= Form::submit('Actualizar', 'class="btn btn-primary"') ?>
<?= Form::close() ?>

<?= Html::link('producto/index', 'Volver', 'class="btn btn-default"') ?>

```

5. **Vista Eliminar**

   La eliminación no necesita una vista específica, ya que es manejada directamente por el controlador.

### Probar la Aplicación

Navega a `http://tu_dominio/producto` para ver la lista de productos y realiza operaciones CRUD para verificar que todo
funciona correctamente.

¡Y eso es todo! Con estos pasos, has integrado ActiveRecord con el sistema de vistas de KumbiaPHP, facilitando el manejo
de modelos y vistas en tu aplicación. 🎉

## Control de acceso basado en roles (RBAC)

El método `initialize` de `AdminController` puede usarse para centralizar la validación de roles y reutilizar la lógica
de control de acceso en todos los controladores que hereden de `AdminController`.

## Verificación de roles en el `AdminController`:
Vamos a definir un `AdminController` que implemente la validación en `initialize`.

  ```php
  class AdminController extends AppController
  {
    protected function initialize()
    {
      // Simulación de usuario autenticado (en un caso real, tomarlo del sistema de autenticación)
      $usuarioAutenticado = Usuario::get(1);

      // Verificar si el usuario tiene rol de 'admin'
      if (!$usuarioAutenticado || !$usuarioAutenticado->tieneRol('admin')) {
         // Redirigir al index si no tiene el rol de administrador
          Redirect::to('index');
      }

      // Establecer el usuario autenticado en los controladores que hereden de AdminController
      $this->usuario = $usuarioAutenticado;
      }
  }
  ```

## Uso en un controlador específico:
Ahora, cualquier controlador que herede de `AdminController` hereda esta lógica de validación, lo que facilita su
reutilización.

  ```php
  class ProductosController extends AdminController
  {
    public function editar($id)
    {
      // La validación de rol se ha hecho en AdminController::initialize
      $producto = Producto::get($id);

      if ($producto) {
        // Acceso permitido para editar el producto
        $this->producto = $producto;
      } else {
        // Redirigir al index si el producto no existe
        Redirect::to('productos/index');
      }
    }
  }
  ```

En este caso, la lógica de control de acceso se aplica en el método initialize de AdminController, permitiendo que
ProductosController y cualquier otro controlador que herede de AdminController comparta la misma validación de roles sin
tener que duplicar el código.