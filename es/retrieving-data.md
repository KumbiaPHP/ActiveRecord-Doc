# Funciones de Consulta con LiteRecord

¡Bienvenidos a la sección donde la magia de las consultas SQL se encuentra con la simplicidad de LiteRecord! Aquí
veremos cómo utilizar las funciones de consulta disponibles en la clase `LiteRecord` con ejemplos significativos.

## Función `get`

La función `get` permite obtener un registro específico de la base de datos utilizando la clave primaria.

### Ejemplo 1: Obtener un producto por su ID

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    public static function obtenerProductoPorID($id)
    {
        return self::get($id);
    }
}

$producto = Producto::obtenerProductoPorID(1);
echo "Producto: $producto->nombre, Precio: $producto->precio";
```

En este ejemplo, obtenemos un producto con ID 1 y mostramos su nombre y precio.

### Ejemplo 2: Obtener un usuario por su ID con campos específicos

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function obtenerUsuarioPorID($id, $fields = 'nombre, email')
    {
        return self::get($id, $fields);
    }
}

$usuario = Usuario::obtenerUsuarioPorID(5);
echo "Usuario: $usuario->nombre}, Email: $usuario->email";
```

Aquí, obtenemos un usuario con ID 5 y mostramos su nombre y email utilizando campos específicos.

### Ejemplo 3: Obtener un objeto por clave primaria utilizando la instancia del modelo

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';
}

$producto = new Producto(3);
echo "Producto: $producto->nombre, Precio: $producto->precio";
```

En este ejemplo, obtenemos un producto con ID 3 utilizando la instancia del modelo y mostramos su nombre y precio.

## Función `all`

La función `all` recupera todos los registros de una tabla, y se puede agregar una cláusula SQL adicional.

### Ejemplo 1: Obtener todos los productos

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    public static function obtenerTodosLosProductos()
    {
        return self::all('SELECT * FROM productos');
    }
}

$productos = Producto::obtenerTodosLosProductos();
foreach ($productos as $producto) {
    echo "Producto: $producto->nombre, Precio: $producto->precio\n";
}
```

En este ejemplo, obtenemos todos los productos y mostramos su nombre y precio.

### Ejemplo 2: Obtener todos los usuarios activos

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function obtenerUsuariosActivos()
    {
        return self::all('SELECT * FROM usuarios WHERE activo = ?', [1]);
    }
}

$usuarios = Usuario::obtenerUsuariosActivos();
foreach ($usuarios as $usuario) {
    echo "Usuario: $usuario->nombre}, Email: $usuario->email\n";
}
```

Aquí, obtenemos todos los usuarios que están activos y mostramos su nombre y email.

## Función `first`

La función `first` recupera el primer registro que coincide con una cláusula SQL específica.

### Ejemplo 1: Obtener el primer producto con precio mayor a 50

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    public static function obtenerPrimerProductoPorPrecio($precio)
    {
        return self::first('SELECT * FROM productos WHERE precio > ?', [$precio]);
    }
}

$producto = Producto::obtenerPrimerProductoPorPrecio(50);
if ($producto) {
    echo "Producto: $producto->nombre, Precio: $producto->precio";
} else {
    echo "No se encontró ningún producto con precio mayor a 50.";
}
```

En este ejemplo, obtenemos el primer producto con un precio mayor a 50.

### Ejemplo 2: Obtener el primer usuario registrado

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function obtenerPrimerUsuarioRegistrado()
    {
        return self::first('SELECT * FROM usuarios ORDER BY fecha_registro ASC');
    }
}

$usuario = Usuario::obtenerPrimerUsuarioRegistrado();
if ($usuario) {
    echo "Primer Usuario: $usuario->nombre, Email: $usuario->email";
} else {
    echo "No se encontró ningún usuario.";
}
```

Aquí, obtenemos el primer usuario registrado ordenado por fecha de registro.

## Función `filter`

La función `filter` permite aplicar filtros personalizados a las consultas SQL y devuelve un array de registros.

### Ejemplo 1: Filtrar productos por rango de precios

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    public static function filtrarProductosPorRangoDePrecios($min, $max)
    {
        return self::filter('WHERE precio BETWEEN ? AND ?', [$min, $max]);
    }
}

$productos = Producto::filtrarProductosPorRangoDePrecios(20, 100);
foreach ($productos as $producto) {
    echo "Producto: $producto->nombre, Precio: $producto->precio\n";
}
```

En este ejemplo, filtramos los productos cuyo precio está entre 20 y 100.

### Ejemplo 2: Filtrar usuarios por ciudad

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function filtrarUsuariosPorCiudad($ciudad)
    {
        return self::filter('WHERE ciudad = ?', [$ciudad]);
    }
}

$usuarios = Usuario::filtrarUsuariosPorCiudad('Madrid');
foreach ($usuarios as $usuario) {
    echo "Usuario: $usuario->nombre, Email: $usuario->email\n";
}
```

Aquí, filtramos los usuarios que viven en Madrid y mostramos su nombre y email.

## Uso de Parámetros Nombrados

LiteRecord también permite el uso de parámetros nombrados en las consultas, lo que facilita la lectura y el mantenimiento del código. Los parámetros nombrados hacen que las consultas sean más claras y ayudan a evitar errores comunes al ordenar los valores de los parámetros.

### Ejemplo de `all` con parámetros nombrados

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function obtenerUsuariosPorCiudadYEstado($ciudad, $estado)
    {
        return self::all(
            'SELECT * FROM usuarios WHERE ciudad = :ciudad AND estado = :estado',
            ['ciudad' => $ciudad, 'estado' => $estado]
        );
    }
}

$usuarios = Usuario::obtenerUsuariosPorCiudadYEstado('Madrid', 'activo');
foreach ($usuarios as $usuario) {
    echo "Usuario: $usuario->nombre, Email: $usuario->email\n";
}
```

### Ejemplo de `first` con parámetros nombrados

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    public static function obtenerPrimerProductoPorCategoria($categoria)
    {
        return self::first(
            'SELECT * FROM productos WHERE categoria = :categoria ORDER BY precio DESC',
            ['categoria' => $categoria]
        );
    }
}

$producto = Producto::obtenerPrimerProductoPorCategoria('Electrónica');
if ($producto) {
    echo "Producto: $producto->nombre, Precio: $producto->precio";
} else {
    echo "No se encontró ningún producto en la categoría Electrónica.";
}
```

### Ejemplo de `filter` con parámetros nombrados

```php
class Usuario extends LiteRecord
{
    protected static $table = 'usuarios';

    public static function filtrarUsuariosPorCiudadYEstado($ciudad, $estado)
    {
        return self::filter(
            'WHERE ciudad = :ciudad AND estado = :estado',
            ['ciudad' => $ciudad, 'estado' => $estado]
        );
    }
}

$usuarios = Usuario::filtrarUsuariosPorCiudadYEstado('Madrid', 'activo');
foreach ($usuarios as $usuario) {
    echo "Usuario: {$usuario->nombre}, Email: {$usuario->email}\n";
}
```

---

¡Y eso es todo! Ahora estás listo para usar las funciones de consulta de `LiteRecord` y hacer magia con tus datos.
¡Felices consultas! 🚀