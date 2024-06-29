# Paginated Queries

In this section, you'll learn how to perform paginated queries to the database using the `paginateQuery` function and
how to manage and display results with the `Paginator` class.

## Introduction

Pagination is an essential technique in web applications that allows large datasets to be divided into smaller, more
manageable parts, improving user experience and application performance. KumbiaPHP's ActiveRecord provides a simple and
efficient solution for pagination, regardless of the database engine.

## `BaseRecord` Class and the `paginateQuery` Function

The `BaseRecord` class in KumbiaPHP includes the static `paginateQuery` function, which allows you to execute SQL
queries and paginate the results easily.

### Definition of `paginateQuery`

```php
public static function paginateQuery(string $sql, int $page, int $perPage, array $values = []): Paginator
{
    //...
}
```

### Parameters

| Parameter         | Description                                      |
|-------------------|--------------------------------------------------|
| `string $sql`     | The SQL query to execute.                        |
| `int $page`       | The page number to retrieve.                     |
| `int $perPage`    | The number of items per page.                    |
| `array $values`   | The values to use in the prepared query.         |

### Return

- `Paginator`: An object of the `Paginator` class that contains the paginated query results.

## `Paginator` Class

The `Paginator` class handles pagination logic and offers useful methods for working with paginated results.

### Definition of the `Paginator` Class

```php
class Paginator implements \IteratorAggregate, \Countable, \JsonSerializable
{
    // Attributes
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
        // Attribute initialization and validations
    }

    // Methods for pagination and data handling
}
```

### Main Methods

| Method          | Description                                                                                               |
|-----------------|-----------------------------------------------------------------------------------------------------------|
| `jsonSerialize` | Allows `json_encode()` to return the items of the paginator.                                              |
| `validPage`     | Verifies that the page and items per page are valid (greater than 0).                                     |
| `validCurrent`  | Validates that the current page does not exceed the total number of pages.                                |
| `getIterator`   | Implements `IteratorAggregate` to allow iteration over the paginator items.                               |
| `countQuery`    | Counts the total number of query results.                                                                 |
| `totalItems`    | Returns the total number of items.                                                                        |
| `totalPages`    | Returns the total number of pages.                                                                        |
| `nextPage`      | Calculates the value of the next page.                                                                    |
| `prevPage`      | Calculates the value of the previous page.                                                                |
| `count`         | Implements `Countable` to count the items in the paginator.                                               |
| `page`          | Returns the current page of the paginator.                                                                |
| `getFields`     | Returns the fields of the paginated objects.                                                              |
| `getAlias`      | Returns the aliases of the paginated objects.                                                             |
| `getPerPage`    | Returns the configured number of items per page.                                                          |

## Practical Example

Here is a practical example of how to use `paginateQuery` and `Paginator` to perform a paginated query and display the
results.

### `User` Class

First, we define a method in the `User` class that performs the paginated query.

```php
class User extends LiteRecord
{
    /**
     * Get paginated users by status.
     *
     * @param string $status
     * @param int $page
     * @param int $perPage
     * @return Paginator
     */
    public static function getPaginatedUsers(string $status, int $page, int $perPage): \Kumbia\ActiveRecord\Paginator
    {
        // SQL query
        $sql = "SELECT * FROM users WHERE status = :status ORDER BY ID DESC";

        // Query parameters
        $values = ['status' => $status];

        // Perform the paginated query
        return self::paginateQuery($sql, $page, $perPage, $values);
    }
}
```

### Using the `getPaginatedUsers` Method

```php
// Pagination parameters
$status = 'active';
$page = 1;
$perPage = 10;

// Get paginated users
$paginator = User::getPaginatedUsers($status, $page, $perPage);

// Display paginated results
foreach ($paginator as $user) {
    echo "ID: $user->id - Name: $user->name<br>";
}

// Display pagination information
echo "Current page: " . $paginator->page() . "<br>";
echo "Total pages: " . $paginator->totalPages() . "<br>";
echo "Total items: " . $paginator->totalItems() . "<br>";
echo "Items per page: " . $paginator->getPerPage() . "<br>";

// Navigation
if ($paginator->prevPage()) {
    echo "<a href='?page=" . $paginator->prevPage() . "'>Previous Page</a> ";
}

if ($paginator->nextPage()) {
    echo "<a href='?page=" . $paginator->nextPage() . "'>Next Page</a>";
}
```

### `Product` Class

Similarly, we define a method in the `Product` class that performs the paginated query.

```php
class Product extends LiteRecord
{
    /**
     * Get paginated products by category.
     *
     * @param int $categoryId
     * @param int $page
     * @param int $perPage
     * @return Paginator
     */
    public static function getPaginatedProducts(int $categoryId, int $page, int $perPage): \Kumbia\ActiveRecord\Paginator
    {
        // SQL query
        $sql = 'SELECT * FROM ' . static::getSource() . ' WHERE category_id = ? ORDER BY ID DESC';

        // Perform the paginated query
        return self::paginateQuery($sql, $page, $perPage, [$categoryId]);
    }
}
```

### Using the `getPaginatedProducts` Method

```php
// Pagination parameters
$category = 2;
$page = 1;
$perPage = 5;

// Get paginated products
$paginator = Product::getPaginatedProducts($category, $page, $perPage);

// Display paginated results
foreach ($paginator as $product) {
    echo "ID: {$product->id} - Name: {$product->name}<br>";
}

// Display pagination information
echo "Current page: " . $paginator->page() . "<br>";
echo "Total pages: " . $paginator->totalPages() . "<br>";
echo "Total items: " . $paginator->totalItems() . "<br>";
echo "Items per page: " . $paginator->getPerPage() . "<br>";

// Navigation
if ($paginator->prevPage()) {
    echo "<a href='?page=" . $paginator->prevPage() . "'>Previous Page</a> ";
}

if ($paginator->nextPage()) {
    echo "<a href='?page=" . $paginator->nextPage() . "'>Next Page</a>";
}
```

## Conclusion

Pagination in ActiveRecord is a powerful and simple feature to implement thanks to the `paginateQuery` function and the
`Paginator` class. With these components, you can perform efficient queries and handle large volumes of data effectively
in your applications.

Always ensure to validate pagination parameters and handle errors properly to offer a robust and smooth user experience.