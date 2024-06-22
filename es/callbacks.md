# Introducción a los Callbacks

En el nuevo ActiveRecord de KumbiaPHP, los callbacks son métodos que se ejecutan en ciertos puntos del ciclo de vida de
un modelo. Permiten ejecutar lógica adicional antes o después de operaciones como crear, actualizar o guardar. Esto es
útil para validar datos, generar registros de auditoría o realizar transformaciones.

## Conceptos y Ejemplos Prácticos

Podemos emplear la clase Logger de KumbiaPHP, que proporciona varios métodos para la depuración y el registro. Ten en
cuenta que puedes utilizar otras bibliotecas de registro según tus preferencias.

### _beforeSave
Este método se ejecuta antes de que el modelo sea guardado, independientemente de si se trata de una creación o una
actualización.
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Validar el precio antes de guardar
    protected function _beforeSave()
    {
        if ($this->precio <= 0) {
            return false;  // Evita guardar el registro si el precio es 0 o negativo
        }
        return true;
    }
}
```

### _afterSave
Se ejecuta después de que el modelo haya sido guardado exitosamente.  
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Registrar el guardado exitoso
    protected function _afterSave()
    {
        Logger::debug("Producto {$this->nombre} guardado correctamente con precio {$this->precio}");
    }
}
```

### _beforeCreate
Se ejecuta antes de que el modelo sea creado. Este callback es útil para agregar información adicional durante la
creación.  
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Añadir la fecha de creación antes de guardar el producto
    protected function _beforeCreate()
    {
        $this->fecha_creacion = date('Y-m-d H:i:s');
        return true;
    }
}
```

### _afterCreate
Se ejecuta después de que el modelo haya sido creado exitosamente.  
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Notificar al usuario después de la creación de un producto
    protected function _afterCreate()
    {
        Logger::debug("Nuevo producto {$this->nombre} creado correctamente.");
    }
}
```

### _beforeUpdate
Se ejecuta antes de que el modelo sea actualizado. Puedes utilizarlo para realizar validaciones adicionales antes de los
cambios.  
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Verificar la disponibilidad de stock antes de actualizar
    protected function _beforeUpdate()
    {
        if ($this->stock < 0) {
            return false;  // Evitar la actualización si el stock es negativo
        }
        return true;
    }
}
```

### _afterUpdate
Se ejecuta después de que el modelo haya sido actualizado correctamente.  
**Ejemplo:**

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Registrar una auditoría después de la actualización
    protected function _afterUpdate()
    {
        Logger::debug("Producto {$this->nombre} actualizado correctamente con stock {$this->stock}.");
    }
}
```

Estos callbacks ayudan a asegurar que la lógica de negocio se aplique en los momentos apropiados del ciclo de vida de
los modelos. ¡Prepárate para hacer magia con tu código!