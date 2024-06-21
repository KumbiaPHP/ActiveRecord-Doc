# Introduction

## What is ActiveRecord in KumbiaPHP?

Welcome to the exciting world of ActiveRecord for KumbiaPHP! 🌟 Imagine handling your databases as easily as playing
with building blocks. ActiveRecord is an implementation of the Active Record design pattern that was born in the
KumbiaPHP ecosystem but now can run freely in any PHP application or framework. With ActiveRecord, you can map PHP
objects directly to database tables, making CRUD operations (Create, Read, Update, Delete) as easy as a walk in the
park. Say goodbye to tedious SQL queries and focus on what really matters: your business logic!

## Advantages and Features

- **Data Abstraction**: Forget about complicated SQL queries. The model automatically translates all your operations
  into SQL.
- **Simplicity**: With predefined methods, CRUD operations are at your fingertips, hassle-free.
- **Validations and Callbacks**: Activate validations and actions at specific moments in your models' lifecycle with
  just a few lines of code.

## Prerequisites

- **PHP**: Make sure you have PHP 7.4 or higher installed.
- **Database**: Compatible with MySQL, PostgreSQL, SQLite, and other PDO-supported database systems.

## Installation and Initial Configuration

- **Installation**: There are two simple ways to install ActiveRecord.
  - **GitHub**: Clone the repository directly from [ActiveRecord on GitHub](https://github.com/KumbiaPHP/ActiveRecord/).
  - **Composer**: Add the package to your `composer.json` file or use the command `composer require kumbia/activerecord`.

- **Configuration**:
  - **Database Connection**: Define your database credentials in your application's configuration file to establish a
    solid connection.
  - **Load the ORM**: Ensure ActiveRecord is correctly loaded in your application's flow, like a VIP guest at an
    exclusive party.
  - **Base Model**: Create a base model that extends from `ActiveRecord` to start defining your specific models and
    unleash the full power of your ORM.