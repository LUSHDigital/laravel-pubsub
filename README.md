# laravel-pubsub

A Pub-Sub abstraction for Laravel.

[![Author](http://img.shields.io/badge/author-@superbalist-blue.svg?style=flat-square)](https://twitter.com/superbalist)
[![Build Status](https://img.shields.io/travis/Superbalist/laravel-pubsub/master.svg?style=flat-square)](https://travis-ci.org/Superbalist/laravel-pubsub)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![Packagist Version](https://img.shields.io/packagist/v/superbalist/laravel-pubsub.svg?style=flat-square)](https://packagist.org/packages/superbalist/laravel-pubsub)
[![Total Downloads](https://img.shields.io/packagist/dt/superbalist/laravel-pubsub.svg?style=flat-square)](https://packagist.org/packages/superbalist/laravel-pubsub)

This package is a wrapper bridging [php-pubsub](https://github.com/Superbalist/php-pubsub) into Laravel.

The following adapters are supported:
* Local
* /dev/null
* Redis
* Kafka
* Google Cloud

## Installation

```bash
composer require superbalist/laravel-pubsub
```

The package has a default configuration which uses the following environment variables.
```
PUBSUB_CONNECTION=redis

REDIS_HOST=localhost
REDIS_PASSWORD=null
REDIS_PORT=6379

KAFKA_BROKERS=localhost

GOOGLE_CLOUD_PROJECT_ID=your-project-id-here
GOOGLE_CLOUD_KEY_FILE=path/to/your/gcloud-key.json
```

To customize the configuration file, publish the package configuration using Artisan.
```bash
php artisan vendor:publish --provider="Superbalist\LaravelPubSub\PubSubServiceProvider"
```

You can then edit the generated config at `app/config/pubsub.php`.

Register the service provider in app.php
```php
'providers' => [
    // ...
    Superbalist\LaravelPubSub\PubSubServiceProvider::class,
]
```

Register the facade in app.php
```php
'aliases' => [
    // ...
    'PubSub' => Superbalist\LaravelPubSub\PubSubFacade::class,
]
```

## Usage

```php
// get the pub-sub manager
$pubsub = app('pubsub');

// note: function calls on the manager are proxied through to the default connection
// eg: you can do this on the manager OR a connection
$pubsub->publish('channel_name', 'message');

// get the default connection
$pubsub = app('pubsub.connection');
// or
$pubsub = app(\Superbalist\PubSub\PubSubAdapterInterface::class);

// get a specific connection
$pubsub = app('pubsub')->connection('redis');

// publish a message
// the message can be a string, array, json string, bool, object - anything which can be serialized
$pubsub->publish('channel_name', 'this is where your message goes');
$pubsub->publish('channel_name', ['key' => 'value']);
$pubsub->publish('channel_name', '{"key": "value"}');
$pubsub->publish('channel_name', true);

// subscribe to a channel
$pubsub->subscribe('channel_name', function ($message) {
    var_dump($message);
});

// all the aboce commands can also be done using the facade
PubSub::connection('kafka')->publish('channel-name', 'Hello World!');

PubSub::connection('kafka')->subscribe('channel_name', function ($message) {
    var_dump($message);
});
```

## TODO

* Documentation
* Unit Tests