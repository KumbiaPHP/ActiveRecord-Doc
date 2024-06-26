# Query Functions with LiteRecord

Welcome to the section where the magic of SQL queries meets the simplicity of LiteRecord! Here we'll explore how to use
the available query functions in the `LiteRecord` class with meaningful examples.

## `get` Function

The `get` function allows you to fetch a specific record from the database using the primary key.

### Example 1: Get a Product by its ID

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getProductByID($id)
    {
        return self::get($id);
    }
}

$product = Product::getProductByID(1);
echo "Product: $product->name, Price: $product->price";
```

In this example, we fetch a product with ID 1 and display its name and price.

### Example 2: Get a User by its ID with Specific Fields

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function getUserByID($id, $fields = 'name, email')
    {
        return self::get($id, $fields);
    }
}

$user = User::getUserByID(5);
echo "User: $user->name, Email: $user->email";
```

Here, we fetch a user with ID 5 and display their name and email using specific fields.

### Example 3: Get an Object by Primary Key Using Model Instance

```php
class Product extends LiteRecord
{
    protected static $table = 'products';
}

$product = new Product(3);
echo "Product: $product->name, Price: $product->price";
```

In this example, we fetch a product with ID 3 using the model instance and display its name and price.

## `filter` Function

The `filter` function allows you to build complex queries without having to write the entire SELECT statement. You only
need to provide the conditions and values.

### Example 1: Filter Products by Price Range

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function filterProductsByPriceRange($min, $max)
    {
        return self::filter('WHERE price BETWEEN ? AND ?', [$min, $max]);
    }
}

$products = Product::filterProductsByPriceRange(20, 100);
foreach ($products as $product) {
    echo "Product: $product->name, Price: $product->price\n";
}
```

In this example, we filter products whose price is between 20 and 100.

### Example 2: Filter Users by City

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function filterUsersByCity($city)
    {
        return self::filter('WHERE city = ?', [$city]);
    }
}

$users = User::filterUsersByCity('Madrid');
foreach ($users as $user) {
    echo "User: $user->name, Email: $user->email\n";
}
```

Here, we filter users who live in Madrid and display their name and email.

### Example 3: Query with JOIN

Suppose we have two tables, `products` and `categories`, and we want to fetch all products along with their category
name.

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getProductsWithCategory()
    {
        return self::filter('JOIN categories ON products.category_id = categories.id');
    }
}

$products = Product::getProductsWithCategory();
foreach ($products as $product) {
    echo "Product: $product->name, Category: $product->category_name\n";
}
```

In this example, we use a `JOIN` to combine data from the `products` and `categories` tables.

### Example 4: Query with ORDER BY

Suppose we want to fetch all products ordered by price in descending order.

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getProductsOrderedByPrice()
    {
        return self::filter('ORDER BY price DESC');
    }
}

$products = Product::getProductsOrderedByPrice();
foreach ($products as $product) {
    echo "Product: $product->name, Price: $product->price\n";
}
```

Here, we use `ORDER BY` to sort products by price in descending order.

### Example 5: Get the First Result with `filter`

Sometimes you only need the first result of a query. You can use `filter` and access the first element of the resulting
array.

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getFirstProductInCategory($categoryId)
    {
        $results = self::filter(
            'WHERE category_id = ? ORDER BY price ASC LIMIT 1',
            [$categoryId]
        );
        return $results[0] ?? null;
    }
}

$product = Product::getFirstProductInCategory(1);
if ($product) {
    echo "Product: $product->name, Price: $product->price\n";
} else {
    echo "No product found in this category.";
}
```

### Example 6: Get a List with `filter`

When you need a complete list that meets certain conditions.

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function getUsersByCityAndStatus($city, $status)
    {
        return self::filter(
            'WHERE city = :city AND status = :status',
            ['city' => $city, 'status' => $status]
        );
    }
}

$users = User::getUsersByCityAndStatus('Madrid', 'active');
foreach ($users as $user) {
    echo "User: $user->name, Email: $user->email\n";
}
```

In this example, we use `filter` to fetch a list of users who live in Madrid and are active.

## `all` Function

The `all` function retrieves all records from a table, and you can add an additional SQL clause.

### Example 1: Get All Products

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getAllProducts()
    {
        return self::all();
    }
}

$products = Product::getAllProducts();
foreach ($products as $product) {
    echo "Product: $product->name, Price: $product->price\n";
}
```

In this example, we fetch all products and display their name and price.

### Example 2: Get All Active Users

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function getActiveUsers()
    {
        return self::all('SELECT * FROM ' . static::getSource() . ' WHERE active = ?', [1]);
    }
}

$users = User::getActiveUsers();
foreach ($users as $user) {
    echo "User: $user->name, Email: $user->email\n";
}
```

Here, we fetch all users who are active and display their name and email.

## `first` Function

The `first` function retrieves the first record that matches a specific SQL clause.

### Example 1: Get the First Product with a Price Greater than 50

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getFirstProductByPrice($price)
    {
        return self::first('SELECT * FROM ' . static::getSource() . ' WHERE price > ?', [$price]);
    }
}

$product = Product::getFirstProductByPrice(50);
if ($product) {
    echo "Product: $product->name, Price: $product->price";
} else {
    echo "No product found with a price greater than 50.";
}
```

In this example, we fetch the first product with a price greater than 50.

### Example 2: Get the First Registered User

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function getFirstRegisteredUser()
    {
        return self::first('SELECT * FROM ' . static::getSource() . ' ORDER BY registration_date ASC');
    }
}

$user = User::getFirstRegisteredUser();
if ($user) {
    echo "First User: $user->name, Email: $user->email";
} else {
    echo "No user found.";
}
```

Here, we fetch the first registered user ordered by registration date.

## Using Named Parameters

LiteRecord also allows the use of named parameters in queries, which makes the code easier to read and maintain. Named
parameters make queries clearer and help avoid common errors when ordering parameter values.

### Example of `all` with Named Parameters

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function getUsersByCityAndStatus($city, $status)
    {
        return self::all(
            'SELECT * FROM ' . static::getSource() . ' WHERE city = :city AND status = :status',
            ['city' => $city, 'status' => $status]
        );
    }
}

$users = User::getUsersByCityAndStatus('Madrid', 'active');
foreach ($users as $user) {
    echo "User: $user->name, Email: $user->email\n";
}
```

### Example of `first` with Named Parameters

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    public static function getFirstProductByCategory($category)
    {
        return self::first

(
            'SELECT * FROM ' . static::getSource() . ' WHERE category = :category ORDER BY price DESC',
            ['category' => $category]
        );
    }
}

$product = Product::getFirstProductByCategory('Electronics');
if ($product) {
    echo "Product: $product->name, Price: $product->price";
} else {
    echo "No product found in the Electronics category.";
}
```

### Example of `filter` with Named Parameters

```php
class User extends LiteRecord
{
    protected static $table = 'users';

    public static function filterUsersByCityAndStatus($city, $status)
    {
        return self::filter(
            'WHERE city = :city AND status = :status',
            ['city' => $city, 'status' => $status]
        );
    }
}

$users = User::filterUsersByCityAndStatus('Madrid', 'active');
foreach ($users as $user) {
    echo "User: $user->name, Email: $user->email\n";
}
```

---

And that's it! Now you're ready to use LiteRecord's query functions and work magic with your data. Happy querying! 🚀