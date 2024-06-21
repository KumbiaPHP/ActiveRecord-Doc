# Integration with KumbiaPHP

## Installation with Composer

To use ActiveRecord in KumbiaPHP, make sure you have [KumbiaPHP](https://kumbiaphp.com/blog/) version 0.9RC or higher.
Here’s a step-by-step guide to installing it using [Composer](https://getcomposer.org).

1. **Create the `composer.json` file at the root of the project if it doesn’t exist:**

   Your project structure should look something like this:

   ```plaintext
   --project  
       |  
       |--vendor  
       |--default  
       |--core  
       |--composer.json        // This is where our file goes
   ```

2. **Add the following code to `composer.json`:**

   ```json
   {
       "require": {
           "kumbia/activerecord": "dev-master"
       }
   }
   ```

3. **Run the `composer install` command:**

   Open a terminal in the root of your project and run:

   ```sh
   composer install
   ```

4. **Use the Composer autoload**

   Since Composer was used to install ActiveRecord, we need to load the Composer autoload within the application. Edit
   the `index.php` file in the `public` folder and add the following lines before loading the KumbiaPHP bootstrap:

   ```php
   require_once '../../vendor/autoload.php';
   ```

5. **Configure the database:**

   Open the database configuration file

   ```plaintext
   vendor/kumbia/activerecord/config/config_databases.php
   ```
   Copy the connection string called `default`, which looks similar to the following:

   ```php
   'default' => [
       'dsn'      => 'mysql:host=localhost;dbname=my_database',
       'username' => 'my_user',
       'password' => 'my_password',
   ],
   ```
   Then, paste it into the `app/config/databases.php` file and modify it with your database credentials.

6. **(Optional) Add files in `app/libs/`:**

   You can choose to add `lite_record.php` and/or `act_record.php` to use `LiteRecord` and `ActiveRecord` respectively.

### LiteRecord

For those who prefer using SQL directly but also want the benefits of a mini-ORM, here’s how to configure `LiteRecord`:

- **Create the `lite_record.php` file in `app/libs/`:**

   ```php
   <?php
   // app/libs/lite_record.php

   /**
    * Record 
    * For those who prefer SQL with the advantages of an ORM
    *
    * Parent class to add your methods
    *
    * @category Kumbia
    * @package ActiveRecord
    * @subpackage LiteRecord
    */

   // use Kumbia\ActiveRecord\LiteRecord as ORM;

   class LiteRecord extends \Kumbia\ActiveRecord\LiteRecord
   {

   }
   ```

### ActRecord

To use the full ActiveRecord, follow these steps:

- **Create the `act_record.php` file in `app/libs/`:**

   ```php
   <?php
   // app/libs/act_record.php

   /**
    * New ActiveRecord
    *
    * Parent class to add your methods
    *
    * @category Kumbia
    * @package ActiveRecord
    * @subpackage ActiveRecord
    */

   // use Kumbia\ActiveRecord\ActiveRecord;

   class ActRecord extends \Kumbia\ActiveRecord\ActiveRecord
   {

   }
   ```

And that's it! Now you have ActiveRecord configured and ready to use in your KumbiaPHP project. 🚀

## Integration with the View System

Integrating ActiveRecord with the KumbiaPHP view system allows you to efficiently handle your models and views,
simplifying the development of web applications. Here’s a complete example to demonstrate how this integration can be
achieved.

### Creating the Model

Let's create a model to handle the products in our database.

- Create the `product.php` file in `app/models/`.

```php
<?php
// app/models/product.php

class Product extends LiteRecord
{
    protected static $table = 'products';

    protected function _beforeCreate()
    {
        // Ensure that the 'price' field in this table is always greater than zero when creating an instance of this
        // class (a record) in the 'products' table.
        return $this->price > 0;
    }
}
```

### Creating the Controller

The controller will handle CRUD operations and pass the data to the views.

- Create the `product_controller.php` file in `app/controllers/`.

```php
<?php
// app/controllers/product_controller.php

class ProductController extends AppController
{
    public function index()
    {
        $this->products = Product::all();
    }

    public function view($id)
    {
        $this->product = Product::get($id);
        if (!$this->product) {
            Flash::error('Product not found');
            return Redirect::to();
        }
    }

    public function create()
    {
        if (Input::hasPost('product')) {
            $product = new Product(Input::post('product'));
            if ($product->create()) {
                Flash::valid('Product created successfully');
                return Redirect::to();
            }
            
            Flash::error('Error creating product');
        }
    }

    public function edit($id)
    {
        $product = Product::get($id);
        if (!$product) {
            Flash::error('Product not found');
            return Redirect::to();
        }

        if (Input::hasPost('product')) {
            if ($product->update(Input::post('product'))) {
                Flash::valid('Product updated successfully');
                return Redirect::to();
            }
            
            Flash::error('Error updating product');
        }

        $this->product = $product;
    }

    public function delete($id)
    {
        if (Product::delete($id)) {
            Flash::valid('Product deleted successfully');
        } else {
            Flash::error('Error deleting product');
        }
        return Redirect::to();
    }
}
```

### Creating the Views

Let's create the views to handle CRUD operations.

1. **Index View**

   Create the `index.phtml` file in `app/views/product/`.

```php
<?php
// app/views/product/index.phtml

echo Html::link('product/create', 'Create new product', 'class="btn btn-primary"');
?>

<?php View::content(); ?>

<table>
    <thead>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Price</th>
        <th>Actions</th>
    </tr>
    </thead>
    <tbody>
    <?php foreach ($products as $product): ?>
        <tr>
            <td><?= $product->id ?></td>
            <td><?= $product->name ?></td>
            <td><?= $product->price ?></td>
            <td>
                <?= Html::link("product/view/{$product->id}", 'View', 'class="btn btn-info"') ?>
                <?= Html::link("product/edit/{$product->id}", 'Edit', 'class="btn btn-warning"') ?>
                <?= Html::link("product/delete/{$product->id}", 'Delete', 'class="btn btn-danger"') ?>
            </td>
        </tr>
    <?php endforeach; ?>
    </tbody>
</table>
```

2. **View View**

   Create the `view.phtml` file in `app/views/product/`.

```php
<?php
// app/views/product/view.phtml
?>

<h1>View Product</h1>
<p><strong>ID:</strong> <?= $product->id ?></p>
<p><strong>Name:</strong> <?= $product->name ?></p>
<p><strong>Price:</strong> <?= $product->price ?></p>

<?= Html::link('product/index', 'Back', 'class="btn btn-primary"') ?>
```

3. **Create View**

   Create the `create.phtml` file in `app/views/product/`.

```php
<?php
// app/views/product/create.phtml
?>

<h1>Create Product</h1>
<?php View::content(); ?>
<?= Form::open() ?>
<div>
    <?= Form::label('Name', 'product_name') ?>
    <?= Form::text('product.name') ?>
</div>
<div>
    <?= Form::label('Price', 'product_price') ?>
    <?= Form::text('product.price') ?>
</div>
<?= Form::submit('Create', 'class="btn btn-primary"') ?>
<?= Form::close() ?>

<?= Html::link('product/index', 'Back', 'class="btn btn-default"') ?>
```

4. **Edit View**

   Create the `edit.phtml` file in `app/views/product/`.

```php
<?php
// app/views/product/edit.phtml
?>

<h1>Edit Product</h1>
<?php View::content(); ?>
<?= Form::open() ?>
<div>
    <?= Form::label('Name', 'product_name') ?>
    <?= Form::text('product.name', $product->name) ?>
</div>
<div>
    <?= Form::label('Price', 'product_price') ?>
    <?= Form::text('product.price', $product->price) ?>
</div>
<?= Form::submit('Update', 'class="btn btn-primary"') ?>
<?= Form::close() ?>

<?= Html::link('product/index', 'Back', 'class="btn btn-default"') ?>
```

5. **Delete View**

   Deletion does not need a specific view as it is handled directly by the controller.

### Testing the Application

Navigate to `http://your_domain/product` to see the list of products and perform CRUD operations to verify that
everything works correctly.

And that’s it! With these steps, you have integrated ActiveRecord with the KumbiaPHP view system, making it easier to
manage models and views in your application. 🎉

## Role-Based Access Control (RBAC)

The `initialize` method of `AdminController` can be used to centralize role validation and reuse access control logic
across all controllers that inherit from `AdminController`.

## Role Verification in `AdminController`:
Let's define an `AdminController` that implements validation in `initialize`.

```php
class AdminController extends AppController
{
    protected function initialize()
    {
        // Simulate authenticated user (in a real case, take it from the authentication system)
        $authenticatedUser = User::get(1);

        // Check if the user has 'admin' role
        if (!$authenticatedUser || !$authenticatedUser->hasRole('admin')) {
            // Redirect to index if not an admin
            Redirect::to('index');
        }

        // Set the authenticated user in the controllers that inherit from AdminController
        $this->user = $authenticatedUser;
    }
}
```

## Usage in a Specific Controller:
Now, any controller that inherits from `AdminController` inherits this validation logic, making it easier to reuse.

```php
class ProductsController extends AdminController
{
    public function edit($id)
    {
        // Role validation has been done in AdminController::initialize
        $product = Product::get($id);

        if ($product) {
            // Access allowed to edit the product
            $this->product = $product;
        } else {
            // Redirect to index if the product does not exist
            Redirect::to('products/index');
        }
    }
}
```

In this case, the access control logic is applied in the `initialize` method of `AdminController`, allowing
`ProductsController` and any other controller that inherits from `AdminController` to share the same role validation
without duplicating code.