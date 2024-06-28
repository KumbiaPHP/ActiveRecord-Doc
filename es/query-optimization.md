# Optimización de Consultas

¡Bienvenidos a la sección donde hacemos que tus consultas vuelen como el viento! Aquí vamos a explorar cómo puedes
optimizar tus consultas en KumbiaPHP para que sean más rápidas y eficientes. Prepárate para ser un maestro de la
optimización de bases de datos.

## Paginación de Resultados

La paginación permite dividir grandes cantidades de datos en partes más pequeñas, facilitando el acceso y presentación
de los resultados.

### Ejemplo de Paginación

Para paginar los resultados, añade un método que limite la cantidad de registros devueltos, comenzando desde un
desplazamiento (`offset`).

```php
class Producto extends LiteRecord
{
    protected static $table = 'productos';

    // Método para obtener productos paginados
    public static function paginar($pagina, $porPagina) {
        $offset = ($pagina - 1) * $porPagina;
        $sql = "LIMIT $porPagina OFFSET $offset";
        return static::filter($sql);
    }
}

// Ejemplo de uso: Página 2 con 10 productos por página
$productos = Producto::paginar(2, 10);
foreach ($productos as $producto) {
    echo $producto->nombre . PHP_EOL;
}
```

### Nota:
- Este ejemplo es básico y necesita verificaciones para evitar tratar de acceder a elementos de páginas que no existen.
  Antes de realizar una consulta, asegúrate de que la página solicitada esté dentro de los límites disponibles,
  calculando el total de registros y dividiéndolo entre la cantidad de resultados por página.
- El tema de la paginación se tratará de forma más completa con la clase `ActiveRecord` una vez esté finalizada, ya que
  proporcionará características adicionales.

## Uso de Índices y Claves Externas

Los índices y las claves externas pueden mejorar el rendimiento de las consultas y asegurar la integridad de los datos.
Asegúrate de que tus columnas clave tengan índices para acelerar búsquedas, y usa claves externas para mantener
relaciones entre tablas.

### Índices

Un índice permite buscar datos más rápidamente. Asegúrate de que las columnas que utilizas frecuentemente en `WHERE` o
`JOIN` tengan índices.

```sql
-- Crear un índice para una columna
CREATE INDEX idx_nombre ON productos (nombre);
```

### Claves Externas

Usa claves externas para asegurar la integridad referencial.

```sql
-- Ejemplo de clave externa entre productos y categorías
ALTER TABLE productos
ADD CONSTRAINT fk_categoria
FOREIGN KEY (categoria_id)
REFERENCES categorias(id);
```

## Mejora del Rendimiento en Consultas Complejas

Para optimizar consultas complejas, intenta lo siguiente:

### Limitación de Campos

Selecciona solo los campos necesarios en lugar de usar `*`.

```php
$producto = Producto::get(1, 'nombre');
```

### Consultas Indexadas

Asegúrate de que las consultas aprovechen los índices de la base de datos. Consulta los planes de ejecución (`EXPLAIN`)
para verificar si un índice está siendo utilizado.

### Consultas Intermedias

Divide las consultas complejas en varias más sencillas para reducir el procesamiento.

### Uso de `JOIN` con Cuidado

Aunque las uniones son útiles, pueden ser costosas si no están bien diseñadas. Considera usar subconsultas o
desnormalizar datos en ciertos casos.

---

¡Y ahí lo tienes! Con estos consejos y ejemplos, puedes optimizar tus consultas en KumbiaPHP para que sean más rápidas y
eficientes. ¡Vamos a seguir construyendo cosas asombrosas juntos! 🚀