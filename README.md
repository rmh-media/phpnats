phpnats
=======

Introduction
------------

A PHP client for the [NATS messaging system](https://nats.io).

Requirements
------------

* php 5.6+
* [gnatsd](https://github.com/apcera/gnatsd)

Usage
-----

### Installation

Let's start by downloading composer into our project dir:

```sh
curl -O http://getcomposer.org/composer.phar
chmod +x composer.phar
```

Now let's tell composer about our project's dependancies, in this case, PHPNats. The way we do this is by creating a composer.json file, and placing it in the root folder of our project, right next to composer.phar

```sh
{
  "require": {
    "rmh-media/phpnats": "dev-master"
  }
}
```

Let's let Composer work its magic:

```sh
php composer.phar install
```

Composer will download all the dependencies defined in composer.json, and prepare all the files needed to autoload them.

### Basic Usage

```php
$client = new \Nats\Connection();
$client->connect();

// Publish Subscribe

// Simple Subscriber.
$client->subscribe(
    'foo',
    function ($message) {
        printf("Data: %s\r\n", $message->getBody());
    }
);

// Simple Publisher.
$client->publish('foo', 'Marty McFly');

// Wait for 1 message.
$client->wait(1);

// Request Response

// Responding to requests.
$sid = $client->subscribe(
    'sayhello',
    function ($message) {
        $message->reply('Reply: Hello, '.$message->getBody().' !!!');
    }
);

// Request.
$client->request(
    'sayhello',
    'Marty McFly',
    function ($message) {
        echo $message->getBody();
    }
);
```

### Encoded Connections

```php
$encoder = new \Nats\Encoders\JSONEncoder();
$options = new \Nats\ConnectionOptions();
$client = new \Nats\EncodedConnection($options, $encoder);
$client->connect();

// Publish Subscribe

// Simple Subscriber.
$client->subscribe(
    'foo',
    function ($payload) {
        printf("Data: %s\r\n", $payload->getBody()[1]);
    }
);

// Simple Publisher.
$client->publish(
    'foo',
    [
     'Marty',
     'McFly',
    ]
);

// Wait for 1 message.
$client->wait(1);

// Request Response

// Responding to requests.
$sid = $client->subscribe(
    'sayhello',
    function ($message) {
        $message->reply('Reply: Hello, '.$message->getBody()[1].' !!!');
    }
);

// Request.
$client->request(
    'sayhello',
    [
     'Marty',
     'McFly',
    ],
    function ($message) {
        echo $message->getBody();
    }
);
```

Developer's Information
-----------------------

### Tests

Tests are in the `tests` folder.
To run them, you need `PHPUnit` and execute `make test-tdd`.

We also have a BDD test suite under the `spec` folder.
To run the suite, you need `PHPSpec` and execute `make test-bdd`.

You can also execute the all suites ( TDD + BDD ) with `make test`.

### Code Quality

We are using [PHP Code Sniffer](http://pear.php.net/package/PHP_CodeSniffer/docs)
to ensure our code follow an high quality standard.

To perform an analysis of the code execute `make lint`.

There is currently three steps when we lint our code:

* First we lint with php itself `php -l`
* Then we lint with PSR2 standard
* And finally we lint with a custom ruleset.xml that checks dockblocks and different performance tips.


Creators
--------

**Raül Pérez**

- <https://twitter.com/repejota>
- <https://github.com/repejota>

License
-------

MIT, see [LICENSE](LICENSE)
