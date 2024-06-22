# Introduction to Callbacks

In the new ActiveRecord of KumbiaPHP, callbacks are methods executed at certain points in a model's lifecycle. They
allow you to run additional logic before or after operations like creating, updating, or saving. This is useful for
validating data, generating audit logs, or performing transformations.

## Concepts and Practical Examples

We can use the Logger class from KumbiaPHP, which provides various methods for debugging and logging. Note that you can
use other logging libraries according to your preferences.

### _beforeSave
This method is executed before the model is saved, regardless of whether it's a creation or an update.
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Validate the price before saving
    protected function _beforeSave()
    {
        if ($this->price <= 0) {
            return false;  // Prevent saving the record if the price is 0 or negative
        }
        return true;
    }
}
```

### _afterSave
This method is executed after the model has been successfully saved.  
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Log the successful save
    protected function _afterSave()
    {
        Logger::debug("Product {$this->name} saved successfully with price {$this->price}");
    }
}
```

### _beforeCreate
This method is executed before the model is created. This callback is useful for adding additional information during
creation.  
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Add the creation date before saving the product
    protected function _beforeCreate()
    {
        $this->created_at = date('Y-m-d H:i:s');
        return true;
    }
}
```

### _afterCreate
This method is executed after the model has been successfully created.  
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Notify the user after a product is created
    protected function _afterCreate()
    {
        Logger::debug("New product {$this->name} created successfully.");
    }
}
```

### _beforeUpdate
This method is executed before the model is updated. You can use it to perform additional validations before the
changes.  
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Check stock availability before updating
    protected function _beforeUpdate()
    {
        if ($this->stock < 0) {
            return false;  // Prevent the update if the stock is negative
        }
        return true;
    }
}
```

### _afterUpdate
This method is executed after the model has been successfully updated.  
**Example:**

```php
class Product extends LiteRecord
{
    protected static $table = 'products';

    // Log an audit entry after the update
    protected function _afterUpdate()
    {
        Logger::debug("Product {$this->name} updated successfully with stock {$this->stock}.");
    }
}
```

These callbacks help ensure that business logic is applied at the appropriate moments in the lifecycle of your models.