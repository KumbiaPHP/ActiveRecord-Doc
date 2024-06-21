# Advanced Configuration

Ready to take your ActiveRecord skills to the next level? Let's explore how you can leverage `LiteRecord` for advanced
implementations like validations, callbacks, and custom filters. Get ready to work some magic with your code!

## Validations and Callbacks

`LiteRecord` allows you to define callbacks to execute actions at specific points in a model's lifecycle. Want to
validate data before saving or log audits after an operation? You can do that!

### Validation Example with a Callback

To validate a product before saving it, define a method called `_beforeSave`.

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Callback before saving
    protected function _beforeSave()
    {
        if ($this->price <= 0) {
            // Do not allow negative or zero prices
            return false;
        }
        return true;  // Proceed with the operation
    }
}

// Usage example
$product = new Product();
$product->name = 'Incorrect Product';
$product->price = -50.00;

if (!$product->save()) {
    echo "The product could not be saved due to a negative price.";
}
```

## Custom Search Filters

Need to implement advanced searches or pagination? `LiteRecord` has you covered with custom SQL queries to efficiently
filter data.

### Filter Example

To search for products within a price range, create a method in the model:

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Custom method to filter by price range
    public static function filterByPrice(float $min, float $max): array
    {
        $sql = "WHERE price BETWEEN ? AND ?";
        return static::filter($sql, [$min, $max]);
    }
}

// Usage example
$products = Product::filterByPrice(10.00, 100.00);
foreach ($products as $product) {
    echo $product->name . " costs " . $product->price . PHP_EOL;
}
```

## Transaction Management

Want to ensure that a series of operations are performed atomically? `LiteRecord` makes it easy to work with
transactions, ensuring data consistency.

```php
try {
    LiteRecord::begin();

    $product1 = Product::get(1);
    $product1->price = 90.00;
    $product1->update();

    $product2 = Product::get(2);
    $product2->price = 80.00;
    $product2->update();

    LiteRecord::commit();  // Confirm the transaction
} catch (Exception $e) {
    LiteRecord::rollback();  // Rollback if an error occurs
    echo "Error during the transaction: " . $e->getMessage();
}
```

And there you have it! With these advanced configurations, you are ready to handle any challenge that comes your way. If
you need more examples or details, feel free to ask. Let's keep building amazing things together! 🚀