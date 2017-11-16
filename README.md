# Optimised binary UUIDs in Laravel Edit

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/laravel-binary-uuid.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-binary-uuid)
[![Build Status](https://img.shields.io/travis/spatie/laravel-binary-uuid/master.svg?style=flat-square)](https://travis-ci.org/spatie/laravel-binary-uuid)
[![SensioLabsInsight](https://img.shields.io/sensiolabs/i/xxxxxxxxx.svg?style=flat-square)](https://insight.sensiolabs.com/projects/xxxxxxxxx)
[![Quality Score](https://img.shields.io/scrutinizer/g/spatie/laravel-binary-uuid.svg?style=flat-square)](https://scrutinizer-ci.com/g/spatie/laravel-binary-uuid)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/laravel-binary-uuid.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-binary-uuid)

This package adds support for optimised binary encoded UUIDs in a MySQL database.

## Installation

You can install the package via composer:

```bash
composer require spatie/laravel-binary-uuid
```

## Usage

Optimised UUIDs are stored as a binary encoded string in the database. 
Besides storing them as binary, some bits are also switched, allowing MySQL to better index them. 
You can read more about this storing mechanism here: [http://mysqlserverteam.com/storing-uuid-values-in-mysql-tables/](http://mysqlserverteam.com/storing-uuid-values-in-mysql-tables/).
 
To let a model make use of these optimised UUIDs, you must add a `uuid` field as the primary field in the table.

```php
Schema::create('table_name', function (Blueprint $table) {
    $table->uuid('uuid');
    $table->primary('uuid');
});
```

Note that the name of the field can be chosen.

In the model, you can use the `HasBinaryUuid` trait which will add the required functionality to support the binary conversion of the UUID.

```php
use Illuminate\Database\Eloquent\Model;
use Spatie\Uuid\HasBinaryUuid;

class TestModel extends Model
{
    use HasBinaryUuid;

    public $incrementing = false;
    protected $primaryKey = 'uuid';
}
```

Please note a few things:

- `$incrementing` must be `false` in order for the UUID to work as your primary key.
- If you're using any other name than `id` for the uuid, you must specify it in `$primaryKey`.

### Querying the model

The most optimal way to query the database is with the binary encoded version of a UUID. 
There's a scope included in the `HasBinaryUuid` trait to do exactly this.

```php
$uuid = 'ff8683dc-cadd-11e7-9547-8c85901eed2e'; // UUID from eg. the URL.

$model = MyModel::withUuid($uuid)->first();
``` 

The `withUuid` scope will automatically encode the UUID string to query the database.
The manual approach would be something like this.

```php
$model = MyModel::where('uuid', MyModel::encodeUuid($uuid))->first();
```

You can also query for multiple UUIDs using the `withUuid` scope.

```php
$models = MyModel::withUuid([
    'ff8683dc-cadd-11e7-9547-8c85901eed2e',
    'ff8683ab-cadd-11e7-9547-8c85900eed2t',
])->get();
```

### Creating

The UUID of a model will automatically be generated upon save.

```php
$model = MyModel::create();

dump($model->uuid); // b"\x11þ╩ÓB#(ªë\x1FîàÉ\x1EÝ." 
```

### Human-readable UUID

UUIDs are only stored as binary in the database. You can however use a textual version for eg. URL generation.

```php
$model = MyModel::create();

dump($model->uuid_text); // "6dae40fa-cae0-11e7-80b6-8c85901eed2e" 
```

If you want to set a specific UUID before creating a model, that's also possible.
It's unlikely though that you'd ever want to do this.

```php
$model = new MyModel();

$model->uuid_text = $uuid;

$model->save();
```

### Testing

``` bash
composer test
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

### Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Postcardware

You're free to use this package, but if it makes it to your production environment we highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

We publish all received postcards [on our company website](https://spatie.be/en/opensource/postcards).

## Credits

- [Brent Roose](https://github.com/brendt)
- [All Contributors](../../contributors)

## Support us

Spatie is a webdesign agency based in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

Does your business depend on our contributions? Reach out and support us on [Patreon](https://www.patreon.com/spatie). 
All pledges will be dedicated to allocating workforce on maintenance and new awesome stuff.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
