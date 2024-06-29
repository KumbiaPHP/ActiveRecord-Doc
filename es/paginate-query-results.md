# Consultas Paginadas

En este apartado, aprenderás a realizar consultas paginadas a la base de datos utilizando la función `paginateQuery` y
cómo administrar y mostrar los resultados con la clase `Paginator`.

## Introducción

La paginación es una técnica esencial en aplicaciones web que permite dividir grandes conjuntos de datos en partes más
manejables, mejorando así la experiencia del usuario y el rendimiento de la aplicación. El ActiveRecord de KumbiaPHP
proporciona una solución sencilla y eficiente para la paginación a través de su sistema sin importar el motor de base de
datos.

## Clase `BaseRecord` y la Función `paginateQuery`

La clase `BaseRecord` en KumbiaPHP incluye la función estática `paginateQuery`, que permite realizar consultas SQL y
paginar los resultados de manera sencilla.

### Definición de `paginateQuery`

```php
public static function paginateQuery(string $sql, int $page, int $perPage, array $values = []): Paginator
{
    //...
}
```

### Parámetros

| Parámetro         | Descripción                                      |
|-------------------|--------------------------------------------------|
| `string $sql`     | La consulta SQL a ejecutar.                      |
| `int $page`       | El número de la página que se desea obtener.     |
| `int $perPage`    | La cantidad de ítems por página.                 |
| `array $values`   | Los valores a utilizar en la consulta preparada. |

### Retorno

- `Paginator`: Un objeto de la clase `Paginator` que contiene los resultados de la consulta paginada.

## Clase `Paginator`

La clase `Paginator` es la encargada de manejar la lógica de paginación y ofrece métodos útiles para trabajar con los
resultados paginados.

### Definición de la Clase `Paginator`

```php
class Paginator implements \IteratorAggregate, \Countable, \JsonSerializable
{
    // Atributos
    protected $page;
    protected $perPage;
    protected $totalPages;
    protected $count;
    protected $model;
    protected $sql;
    protected $values;
    private $items;

    // Constructor
    public function __construct(string $model, string $sql, int $page, int $perPage, array $values = [])
    {
        // Inicialización de atributos y validaciones
    }

    // Métodos para paginación y manejo de datos
}
```

### Métodos Principales

| Método          | Descripción                                                                                               |
|-----------------|-----------------------------------------------------------------------------------------------------------|
| `jsonSerialize` | Permite que al usar `json_encode()` con una instancia de `Paginator` se retornen los ítems del paginador. |
| `validPage`     | Verifica que la página y la cantidad de ítems por página sean válidos (mayores a 0).                      |
| `validCurrent`  | Valida que la página actual no exceda el número total de páginas.                                         |
| `getIterator`   | Implementa `IteratorAggregate` para permitir la iteración sobre los ítems del paginador.                  |
| `countQuery`    | Cuenta el número total de resultados de la consulta.                                                      |
| `totalItems`    | Retorna el número total de ítems.                                                                         |
| `totalPages`    | Retorna el número total de páginas.                                                                       |
| `nextPage`      | Calcula el valor de la próxima página.                                                                    |
| `prevPage`      | Calcula el valor de la página anterior.                                                                   |
| `count`         | Implementa `Countable` para contar los ítems del paginador.                                               |
| `page`          | Retorna la página actual del paginador.                                                                   |
| `getFields`     | Retornan los campos de los objetos paginados.                                                             |
| `getAlias`      | Retornan los alias de los objetos paginados.                                                              |
| `getPerPage`    | Retorna la cantidad de ítems por página configurados.                                                     |

## Ejemplo Práctico

A continuación, se muestra un ejemplo práctico de cómo usar `paginateQuery` y `Paginator` para realizar una consulta
paginada y mostrar los resultados.

### Clase `Usuario`

Primero, definimos un método en la clase `Usuario` que realiza la consulta paginada.

```php
class Usuario extends LiteRecord
{
    /**
     * Obtener usuarios paginados por estado.
     *
     * @param string $estado
     * @param int $page
     * @param int $perPage
     * @return Paginator
     */
    public static function obtenerUsuariosPaginados(string $estado, int $page, int $perPage): \Kumbia\ActiveRecord\Paginator
    {
        // Consulta SQL
        $sql = "SELECT * FROM usuarios WHERE estado = :estado ORDER BY ID DESC";

        // Parámetros de la consulta
        $values = ['estado' => $estado];

        // Realizar la consulta paginada
        return self::paginateQuery($sql, $page, $perPage, $values);
    }
}
```

### Uso del Método `obtenerUsuariosPaginados`

```php
// Parámetros para la paginación
$estado = 'activo';
$page = 1;
$perPage = 10;

// Obtener usuarios paginados
$paginator = Usuario::obtenerUsuariosPaginados($estado, $page, $perPage);

// Mostrar los resultados paginados
foreach ($paginator as $usuario) {
    echo "ID: $usuario->id - Nombre: $usuario->nombre<br>";
}

// Mostrar información de paginación
echo "Página actual: " . $paginator->page() . "<br>";
echo "Total de páginas: " . $paginator->totalPages() . "<br>";
echo "Total de ítems: " . $paginator->totalItems() . "<br>";
echo "Ítems por página: " . $paginator->getPerPage() . "<br>";

// Navegación
if ($paginator->prevPage()) {
    echo "<a href='?page=" . $paginator->prevPage() . "'>Página Anterior</a> ";
}

if ($paginator->nextPage()) {
    echo "<a href='?page=" . $paginator->nextPage() . "'>Página Siguiente</a>";
}
```

### Clase `Producto`

De manera similar, definimos un método en la clase `Producto` que realiza la consulta paginada.

```php
class Producto extends LiteRecord
{
    /**
     * Obtener productos paginados por categoría.
     *
     * @param int $categoriaId
     * @param int $page
     * @param int $perPage
     * @return Paginator
     */
    public static function obtenerProductosPaginados(int $categoriaId, int $page, int $perPage): \Kumbia\ActiveRecord\Paginator
    {
        // Consulta SQL
        $sql = 'SELECT * FROM ' . static::getSource(). ' WHERE categoria_id = ? ORDER BY ID DESC';

        // Realizar la consulta paginada
        return self::paginateQuery($sql, $page, $perPage, [$categoriaId]);
    }
}
```

### Uso del Método `obtenerProductosPaginados`

```php
// Parámetros para la paginación
$categoria = 2;
$page = 1;
$perPage = 5;

// Obtener productos paginados
$paginator = Producto::obtenerProductosPaginados($categoria, $page, $perPage);

// Mostrar los resultados paginados
foreach ($paginator as $producto) {
    echo "ID: {$producto->id} - Nombre: {$producto->nombre}<br>";
}

// Mostrar información de paginación
echo "Página actual: " . $paginator->page() . "<br>";
echo "Total de páginas: " . $paginator->totalPages() . "<br>";
echo "Total de ítems: " . $paginator->totalItems() . "<br>";
echo "Ítems por página: " . $paginator->getPerPage() . "<br>";

// Navegación
if ($paginator->prevPage()) {
    echo "<a href='?page=" . $paginator->prevPage() . "'>Página Anterior</a> ";
}

if ($paginator->nextPage()) {
    echo "<a href='?page=" . $paginator->nextPage() . "'>Página Siguiente</a>";
}
```

## Conclusión

La paginación en el ActiveRecord es una funcionalidad poderosa y sencilla de implementar gracias a la función
`paginateQuery` y la clase `Paginator`. Con estos componentes, puedes realizar consultas eficientes y manejar grandes
volúmenes de datos de manera efectiva en tus aplicaciones.

Asegúrate de validar siempre los parámetros de la paginación y manejar adecuadamente los errores para ofrecer una
experiencia de usuario robusta y sin problemas.