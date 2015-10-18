# Doctrine Migrations

[![Build Status](https://img.shields.io/travis/Zenify/DoctrineMigrations.svg?style=flat-square)](https://travis-ci.org/Zenify/DoctrineMigrations)
[![Quality Score](https://img.shields.io/scrutinizer/g/Zenify/DoctrineMigrations.svg?style=flat-square)](https://scrutinizer-ci.com/g/Zenify/DoctrineMigrations)
[![Code Coverage](https://img.shields.io/scrutinizer/coverage/g/Zenify/DoctrineMigrations.svg?style=flat-square)](https://scrutinizer-ci.com/g/Zenify/DoctrineMigrations)
[![Downloads this Month](https://img.shields.io/packagist/dt/zenify/doctrine-migrations.svg?style=flat-square)](https://packagist.org/packages/zenify/doctrine-migrations)
[![Latest stable](https://img.shields.io/packagist/v/zenify/doctrine-migrations.svg?style=flat-square)](https://packagist.org/packages/zenify/doctrine-migrations)

Implementation of [Doctrine\Migrations](http://docs.doctrine-project.org/projects/doctrine-migrations/en/latest/) to Nette.


## Install

Install via Composer:

```sh
composer require zenify/doctrine-migrations
```

Register extensions in `config.neon`:

```yaml
extensions:
	migrations: Zenify\DoctrineMigrations\DI\MigrationsExtension
	# Kdyby\Doctrine or another Doctrine integration
	- Kdyby\Annotations\DI\AnnotationsExtension
    - Kdyby\Console\DI\ConsoleExtension
    - Kdyby\Events\DI\EventsExtension
    doctrine: Kdyby\Doctrine\DI\OrmExtension
```


## Configuration

`config.neon` with default values

```yaml
migrations:
	table: doctrine_migrations # database table for applied migrations
	dirs: # list of dirs to load migrations from
		- %appDir%/../migrations # first dir is used for generating migrations
		- %appDir%/../migrations/2014
		- %appDir%/../migrations/setup
	namespace: Migrations # namespace of migration classes
	codingStandard: tabs # or "spaces", coding style for generated classes
```


## Usage

Open your CLI and run command (based on `Kdyby\Console` integration):

```sh
php www/index.php
```

And then you should see all available commands:

![CLI commands](cli-commands.png)



### Migrate changes to database

If you want to migrate existing migration to your database, just run migrate commmand:
 
```sh
php www/index.php migrations:migrate
```

If you get lost, just use `-h` option for help:

```sh
php www/index.php migrations:migrate -h
```

### Create new migration

To create new empty migration, just run:

```sh
php www/index.php migrations:generate
```

A new empty migration will be created at your migrations directory. You can add your sql there then.

Migration that would add new role `"superadmin"` to `user_role` table would look like this:

```php
namespace Migrations;

use Doctrine\DBAL\Migrations\AbstractMigration;
use Doctrine\DBAL\Schema\Schema;


/**
 * New role "superadmin" added.
 */
final class Version20151015000003 extends AbstractMigration
{

	/**
	 * {@inheritdoc}
	 */
	public function up(Schema $schema)
	{
		$this->addSql("INSERT INTO 'user_role' (id, value, name) VALUES (3, 'superadmin', 'Super Admin')");
	}
	

	/**
	 * {@inheritdoc}
	 */
	public function down(Schema $schema)
	{
		$this->addSql("DELETE FROM 'user_role' WHERE ('id' = 3);");
	}

}
```

Simple as that!


For further use, please check [docs in Symfony bundle](http://symfony.com/doc/current/bundles/DoctrineMigrationsBundle/index.html).


## Features


### Injected migrations

Note: this is not really best practise, so try to use it only if there is no other way.

```php
namespace Migrations;


final class Version20140801152432 extends AbstractMigration
{

	/**
	 * @inject
	 * @var Doctrine\ORM\EntityManagerInterface
	 */
	public $entityManager;


	public function up(Schema $schema)
	{
		// ...
	}

	// ...

}
```
