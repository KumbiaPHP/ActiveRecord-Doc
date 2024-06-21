# Basic Usage

The new ActiveRecord system includes two main classes: `ActiveRecord` (full functionality) and `LiteRecord` (for those
who prefer using SQL while still reaping the benefits of an ORM). Since `ActiveRecord` is still under development, it is
recommended to use `LiteRecord` for most current implementations.

## Fundamental Concepts of LiteRecord

`LiteRecord` provides a reduced set of features that allows you to use SQL queries directly, but also offers the
simplicity and efficiency of an ORM. Each model class represents a database table, and each instance of that class
refers to a specific row.

## Defining Models with LiteRecord

To define a model with `LiteRecord`, create a PHP class that extends from `LiteRecord`.

```php
// Example model called Product using LiteRecord
class Product extends LiteRecord
{
    // Optional: Specify the table name if different from the lowercase, pluralized class name
    protected static $table = 'products';

    // Optional: Define the primary key field if different from "id"
    protected static $pk = 'product_id';
}
```

## Basic CRUD Operations with LiteRecord

### Create

You can use the `create()` method directly with an array of data, or set the object's attribute values individually
before calling the `create()` method to insert the record into the database.

```php
// Use the create method directly with an array of data
$product = new Product();
$product->create([
    'name' => 'New Product',
    'price' => 100.50
]);

// Or set the attributes individually and then call create
$product = new Product();
$product->name = 'New Product';
$product->price = 100.50;
$product->create();
```

**Differences between both approaches:**

- **`create` method with array of data:**  
  In this approach, data is provided in a single step. This is more direct and can be convenient if you already have all
  the values available in a structure like an associative array.

- **Individual attributes followed by `create`:**  
  Here, the object's attributes are set separately, providing more flexibility to manipulate them before performing the
  insertion. This allows for more operations before insertion, such as validations or data transformations.

Both methods offer distinct advantages depending on the use case. Using a combination of both based on your
application's needs can help maintain clear and efficient code.

### Read

Records can be retrieved with methods like `get()`, `all()`, or `first()`.

```php
// Get a product by its ID
$product = Product::get(1);

// Get all products
$products = Product::all();
```

### Update

To update a record, load the object, modify the desired fields, and call the `update()` method.

```php
$product = Product::get(1);
if ($product) {
    $product->update([
        'name' => 'Updated Product',
        'price' => 120.00
    ]);
}

// Or set the attributes individually and then call update
$product = Product::get(1);
if ($product) {
    // Modify the attributes individually
    $product->name = 'Updated Product';
    $product->price = 120.00;

    // Save the changes
    $product->update();
}
```

### Delete

Records are deleted using the `delete()` method with the primary key.

```php
Product::delete(1);
```

LiteRecord offers the benefits of a mini-ORM with the flexibility to write custom SQL queries. It's like having the best
of both worlds, making your work easier and maximizing your productivity! 🚀