# Configuración Avanzada

¿Listo para llevar tus habilidades con ActiveRecord al siguiente nivel? Vamos a explorar cómo puedes aprovechar
`LiteRecord` para implementaciones avanzadas como validaciones, callbacks y filtros personalizados. ¡Prepárate para
hacer magia con tu código!

## Validaciones y callbacks

`LiteRecord` te permite definir callbacks para ejecutar acciones en momentos específicos del ciclo de vida de un modelo.
¿Quieres validar datos antes de guardar o registrar auditorías después de una operación? ¡Puedes hacerlo!

### Ejemplo de validación con un callback

Para validar un producto antes de guardarlo, define un método llamado `_beforeSave`.

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Callback antes de guardar
    protected function _beforeSave()
    {
        if ($this->precio <= 0) {
            // No permitir precios negativos o cero
            return false;
        }
        return true;  // Continuar con la operación
    }
}

// Ejemplo de uso
$producto = new Producto();
$producto->nombre = 'Producto Incorrecto';
$producto->precio = -50.00;

if (!$producto->save()) {
    echo "No se pudo guardar el producto debido a un precio negativo.";
}
```

## Filtros de búsqueda personalizados

¿Necesitas implementar búsquedas avanzadas o paginación? `LiteRecord` te tiene cubierto con consultas SQL personalizadas
para filtrar datos de manera eficiente.

### Ejemplo de filtro

Para buscar productos con un precio dentro de un rango, crea un método en el modelo:

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Método personalizado para filtrar por rango de precios
    public static function filtrarPorPrecio(float $min, float $max): array
    {
        $sql = "WHERE precio BETWEEN ? AND ?";
        return static::filter($sql, [$min, $max]);
    }
}

// Ejemplo de uso
$productos = Producto::filtrarPorPrecio(10.00, 100.00);
foreach ($productos as $producto) {
    echo $producto->nombre . " cuesta " . $producto->precio . PHP_EOL;
}
```

## Gestión de transacciones

¿Quieres asegurarte de que una serie de operaciones se realicen de forma atómica? `LiteRecord` facilita el trabajo con
transacciones, garantizando la consistencia de los datos.

```php
try {
    LiteRecord::begin();

    $producto1 = Producto::get(1);
    $producto1->precio = 90.00;
    $producto1->update();

    $producto2 = Producto::get(2);
    $producto2->precio = 80.00;
    $producto2->update();

    LiteRecord::commit();  // Confirma la transacción
} catch (Exception $e) {
    LiteRecord::rollback();  // Revertir si ocurre un error
    echo "Error durante la transacción: " . $e->getMessage();
}
```

¡Y ahí lo tienes! Con estas configuraciones avanzadas, estás listo para manejar cualquier desafío que se presente en tu
camino. Si necesitas más ejemplos o detalles, no dudes en preguntar. ¡Vamos a seguir construyendo cosas asombrosas
juntos! 🚀