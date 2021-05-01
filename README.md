Debug
===============
Support php version >= 5

Installation
------------

Add Debug as a requirement in your `composer.json` file or run
```sh
$ composer require yapro/debug dev-php5
```

For Symfony >= 2.x
------------

It is a collection of monolog processors, that gives you the opportunity to handle and log different errors.

Add needed for you services to file app/config/config.yml
```yml
services:
    monolog.processor.debug:
        class: Debug\Monolog\Processor\Debug
        tags:
            - { name: monolog.processor, handler: main }

    monolog.processor.guzzle:
        class: Debug\Monolog\Processor\Guzzle
        tags:
            - { name: monolog.processor, handler: main }

    monolog.processor.request_as_curl:
        class: Debug\Monolog\Processor\RequestAsCurl
        arguments:  ["@request_stack"]
        tags:
            - { name: monolog.processor, handler: main }

    symfony.listener.command_exception:
         class: Debug\Symfony\ConsoleExceptionListener
         arguments: ['@logger']
         tags:
            - { name: kernel.event_listener, event: console.exception }

    symfony.listener.command_error:
         class: Debug\Symfony\ErrorLoggerListener
         arguments: ['@logger']
         tags:
            - { name: kernel.event_listener, event: console.terminate }
```
and then use logger service, examples:

```php
public function indexAction()
{
    $logger = $this->get('logger');
    $logger->info('I just got the logger');
    $logger->error('An error occurred');
```
Functionality of monolog.processor.debug: all of the above described methods will to write a stack trace of call place
```php
    $e = new \Exception('Something wrong');
    // or
    $e = (new \ExtraException('Something wrong'))->setCustomTrace('My\nTrace')->setCode('My value');

    $logger->notice($e, array(
       'my' => 'data',// some custom data
    ));
```
Look up, variable $e will be serialized to string (Monolog`s functionality) and you will get: Message of Exception + Stack trace
```php
    $logger->warning('My error', array(
       'my' => 'data',
       'exception' => $e,// now you can see the above written custom stack trace as a string
    ));

    $logger->warning('My error', array($e));// the short variant of version which you can see the above
}
```
By default \Debug\Monolog\Processor\Debug extract a extra data into string by standard depth's level which is equal
to two. But, you can use any depth's level, example is equal a five:
```php
    $logger->error('An error occurred', [ 'my myltidimantion array' => \Debug\DebugUtility::export($myArray, 5) ] );
```

For projects without Symfony 2 framework.
------------

Debug is a [Monolog Cascade](https://github.com/theorchard/monolog-cascade) extension that which gives you the opportunity to handle and log errors of different levels.

### Usage

Just use your logger as shown below
```php
Cascade::fileConfig($config);
Log::info('Well, that works!');
Log::error('Maybe not...', ['some'=>'extra data']);
```

### Configuring your loggers

Monolog Cascade supports the following config formats:
 - Yaml
 - JSON
 - Php array

### Configuration structure

Here is a sample Php array config file:

```php
<?php

$config = [
    'formatters' => [
        'dashed' => [
            //'class' => 'Monolog\Formatter\LineFormatter',
            'class' => \Monolog\Formatter\JsonFormatter::class
            //'format' => '%datetime%-%channel%.%level_name% - %message%'
        ]
    ],
    'handlers' => [
        'console' => [
            'class' => 'Monolog\Handler\StreamHandler',
            'level' => 'DEBUG',
            'formatter' => 'dashed',
            'stream' => 'php://stdout'
        ],
        'info_file_handler' => [
            'class' => 'Monolog\Handler\StreamHandler',
            'level' => 'INFO',
            'formatter' => 'dashed',
            'stream' => './example_info.log'
        ]
    ],
    'processors' => [
        'web_processor' => [
            'class' => 'Monolog\Processor\WebProcessor'
        ]
    ],
    'loggers' => [
        'mainLogger' => [
            'handlers' => [
                0 => 'console',
                1 => 'info_file_handler'
            ],
            'processors' => [
                0 => 'web_processor'
            ]
        ]
    ],
    'disable_existing_loggers' => true,
    'errorReporting' => E_ALL & ~E_DEPRECATED & ~E_STRICT,
];
```

More detailed information about the configurations - https://github.com/theorchard/monolog-cascade


What is ExtraException
------------------------

ExtraException is exception which you can to create as object, to add the extra data and throw away. After throwing the Debugger will catches this exception and saves extra data to logs. Examples:

```php
throw (new ExtraException())->setExtra('mixed data');
```
or:
```php
try {
    ...
} catch (\Exception $e) {
    throw (new ExtraException())->setCustomTrace($e->getTraceAsString());
```

Recomendation
------------------------
Add service json_formatter to file app/config/config.yml
It will help you to format error in the json, and then you can use https://www.elastic.co/products/kibana for aggregate all errors.
```yml
services:
    json_formatter:
        class: Monolog\Formatter\JsonFormatter
```
And don`t forget to add a monolog formatter:
```yml
monolog:
    handlers:
        main:
            formatter: json_formatter
```
If you wish to collect some data of http request, you can add WebProcessor:
```yml
services:
    monolog.processor.web:
        class: Monolog\Processor\WebProcessor
        tags:
            - { name: monolog.processor, handler: main }
```
