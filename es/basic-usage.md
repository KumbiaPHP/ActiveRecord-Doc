# Uso Básico

El nuevo sistema ActiveRecord incluye dos clases principales: `ActiveRecord` (con funcionalidad completa) y `LiteRecord`
(para aquellos que prefieren usar SQL y obtener las ventajas de un ORM). Dado que `ActiveRecord` aún está en desarrollo,
se recomienda usar `LiteRecord` para la mayoría de las implementaciones actuales.

## Conceptos fundamentales de LiteRecord

`LiteRecord` proporciona un conjunto reducido de características que permite utilizar consultas SQL directamente, pero
también ofrece la simplicidad y eficiencia de un ORM. Cada clase de modelo representa una tabla en la base de datos, y
cada instancia de esa clase se refiere a una fila específica.

## Definición de modelos con LiteRecord

Para definir un modelo con `LiteRecord`, crea una clase PHP que extienda de `LiteRecord`.

```php
// Modelo de ejemplo llamado Producto usando LiteRecord
class Producto extends LiteRecord
{
    // Opcional: Indica el nombre de la tabla si es diferente al nombre de la clase en minúsculas y plural
    protected static $table = 'productos';

    // Opcional: Define el campo de clave primaria si es diferente de "id"
    protected static $pk = 'producto_id';
}
```

## Operaciones CRUD básicas con LiteRecord

### Crear (Create)

Puedes usar el método `create()` directamente con un array de datos, o establecer los valores de los atributos del
objeto individualmente antes de llamar al método `create()` para insertar el registro en la base de datos.

```php
// Usar directamente el método create con un array de datos
$producto = new Producto();
$producto->create([
    'nombre' => 'Nuevo Producto',
    'precio' => 100.50
]);

// O establecer los atributos individualmente y luego llamar a create
$producto = new Producto();
$producto->nombre = 'Nuevo Producto';
$producto->precio = 100.50;
$producto->create();
```

**Diferencias entre ambos enfoques:**

- **Método `create` con array de datos:**  
  En este enfoque, se proporcionan los datos en un solo paso. Esto es más directo y puede ser conveniente si ya tienes
  todos los valores disponibles en una estructura de datos como un array asociativo.

- **Atributos individuales seguidos de `create`:**  
  Aquí, los atributos del objeto se establecen por separado, proporcionando más flexibilidad para manipularlos antes de
  realizar la inserción. Esto permite realizar más operaciones antes de la inserción, como validaciones o
  transformaciones de los datos.

Ambos métodos ofrecen ventajas distintas según el caso de uso. Usar una combinación de ambos según las necesidades de tu
aplicación puede ayudar a mantener un código claro y eficaz.

### Leer (Read)

Los registros pueden obtenerse con métodos como `get()`, `all()` o `first()`.

```php
// Obtener un producto por su ID
$producto = Producto::get(1);

// Obtener todos los productos
$productos = Producto::all();
```

### Actualizar (Update)

Para actualizar un registro, carga el objeto, modifica los campos deseados y llama al método `update()`.

```php
$producto = Producto::get(1);
if ($producto) {
    $producto->update([
        'nombre' => 'Producto Actualizado',
        'precio' => 120.00
    ]);
}

// O establecer los atributos individualmente y luego llamar a update
$producto = Producto::get(1);
if ($producto) {
    // Modificar los atributos individualmente
    $producto->nombre = 'Producto Actualizado';
    $producto->precio = 120.00;

    // Guardar los cambios
    $producto->update();
}
```

### Eliminar (Delete)

La eliminación de registros se realiza usando el método `delete()` con la clave primaria.

```php
Producto::delete(1);
```

### Guardar (Save)

El método `save()` de LiteRecord permite crear o actualizar un registro dependiendo de si tiene valor en la clave
primaria o no. Si el objeto tiene una clave primaria establecida, realiza una actualización, de lo contrario, crea un
nuevo registro.

```php
// Crear un nuevo producto
$producto = new Producto();
$producto->nombre = 'Producto Nuevo';
$producto->precio = 100.50;
$producto->save();  // Se realiza un create

// Actualizar un producto existente
$producto = Producto::get(1);
$producto->precio = 120.00;
$producto->save();  // Se realiza un update
```

También se puede usar directamente el método save con un array de datos.

```php
// Crear un nuevo producto
$producto = new Producto();
$producto->save([    
    'nombre' => 'Producto Nuevo',
    'precio' => 100.50
]); // Se realiza un create

// Actualizar un producto existente
$producto = new Producto();
$producto->save([
    'id' => 1,
    'nombre' => 'Producto Actualizado',
    'precio' => 120.00
]);  // Se realiza un update
```

LiteRecord ofrece los beneficios de un mini-ORM con la flexibilidad de escribir consultas SQL personalizadas. ¡Es como
tener lo mejor de ambos mundos, facilitando tu trabajo y maximizando tu productividad! 🚀