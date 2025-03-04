---
title: Message broker
description: Follow these steps to install and configure required message broker software (such as RabbitMQ) for on-premises installations of Adobe Commerce and Magento Open Source.
---

# Message broker

Adobe Commerce uses the RabbitMQ open-source message broker. It offers a reliable, highly available, scalable, and portable messaging system.

Message queues provide an asynchronous communications mechanism in which the sender and the receiver of a message do not contact each other. Nor do they need to communicate with the message queue at the same time. When a sender places a message in a queue, it is stored until the recipient receives them.

The message queue system must be established before you install Adobe Commerce or Magento Open Source. The basic sequence is:

1. Install RabbitMQ and any prerequisites.
1. Connect RabbitMQ to Adobe Commerce or Magento Open Source.

>[!NOTE]
>
>You can use MySQL or RabbitMQ for message queue processing. For details on setting up the message queue system, see [Message queues overview](https://developer.adobe.com/commerce/php/development/components/message-queues/). If you are using the Bulk API with Adobe Commerce, the message queue system configuration defaults to using RabbitMQ as the message broker. See [Start message queue consumers](../../configuration/cli/start-message-queues.md) for more information.

## Install RabbitMQ on Ubuntu

To install RabbitMQ on Ubuntu 16, enter the following command:

```bash
sudo apt install -y rabbitmq-server
```

This command also installs the required Erlang packages.

If you have an older version of Ubuntu, RabbitMQ recommends installing the package from their website.

1. Download the .deb package from [rabbitmq-server](https://www.rabbitmq.com/download.html).
1. Install the package with `dpkg`.

Refer to [Installing on Debian/Ubuntu](https://www.rabbitmq.com/install-debian.html) for more information.

## Install RabbitMQ on CentOS

### Install Erlang

RabbitMQ was written using the Erlang programming language, which must be installed on the same system as RabbitMQ.

See [Manual installation](https://www.erlang-solutions.com/downloads/) for more information.

Refer to the [RabbitMQ/Erlang version matrix](https://www.rabbitmq.com/which-erlang.html) to install the correct version.

### Install RabbitMQ

The RabbitMQ server is included on CentOS, but the version is often old. RabbitMQ recommends installing the package from their website.

Refer to the RabbitMQ install page to get the latest supported version. Adobe Commerce and Magento Open Source 2.3 and 2.4 support RabbitMQ 3.8.x.

Refer to [Installing on RPM-based Linux](https://www.rabbitmq.com/install-rpm.html) for more information.

## Configure RabbitMQ

Review the official RabbitMQ documentation to configure and manage RabbitMQ. Pay attention to the following items:

*  Environment variables
*  Port access
*  Default user accounts
*  Starting and stopping the broker
*  System limits

## Install with RabbitMQ and connect

If you install Adobe Commerce or Magento Open Source _after_ you install RabbitMQ, add the following command-line parameters during installation:

```bash
--amqp-host="<hostname>" --amqp-port="5672" --amqp-user="<user_name>" --amqp-password="<password>" --amqp-virtualhost="/"
```

Where:

|Parameter|Description|
|--- |--- |
|`--amqp-host`|The hostname where RabbitMQ is installed.|
|`--amqp-port`|The port to use to connect to RabbitMQ. The default is `5672`.|
|`--amqp-user`|The username for connecting to RabbitMQ. Do not use the default user `guest`.|
|`--amqp-password`|The password for connecting to RabbitMQ. Do not use the default password `guest`.|
|`--amqp-virtualhost`|The virtual host for connecting to RabbitMQ. The default is `/`.|
|`--amqp-ssl`|Indicates whether to connect to RabbitMQ. The default is `false`. If you set the value to true, see Configure SSL for more information.|

## Connect RabbitMQ

If you already had Adobe Commerce or Magento Open Source installed and you want to connect it to RabbitMQ, add a `queue` section in the `<install_directory>/app/etc/env.php` file so that it is similar to the following:

```php
'queue' =>
  array (
    'amqp' =>
    array (
      'host' => 'rabbitmq.example.com',
      'port' => '11213',
      'user' => 'magento',
      'password' => 'magento',
      'virtualhost' => '/'
     ),
  ),
```

You can also set RabbitMQ configuration values using the `bin/magento setup:config:set` command:

```bash
bin/magento setup:config:set --amqp-host="rabbitmq.example.com" --amqp-port="11213" --amqp-user="magento" --amqp-password="magento" --amqp-virtualhost="/"
```

After the running the command or updating the `<install_directory>/app/etc/env.php` file with AMQP configuration values, run `bin/magento setup:upgrade` to apply the changes and create the required queues and exchanges in RabbitMQ.

## Configure SSL

To configure support for SSL, edit the `ssl` and `ssl_options` parameters in the `<install_directory>/app/etc/env.php` file so that they are similar to the following:

```php
'queue' =>
  array (
    'amqp' =>
    array (
      'host' => 'rabbitmq.example.com',
      'port' => '11213',
      'user' => 'magento',
      'password' => 'magento',
      'virtualhost' => '/',
      'ssl' => 'true',
      'ssl_options' => [
            'cafile' =>  '/etc/pki/tls/certs/DigiCertCA.crt',
            'certfile' => '/path/to/magento/app/etc/ssl/test-rabbit.crt',
            'keyfile' => '/path/to/magento/app/etc/ssl/test-rabbit.key'
       ],
     ),
  ),
```

## Start the message queue consumers

After you have connected Adobe Commerce and RabbitMQ, you must start the message queue consumers. See [Configure message queues](../../configuration/cli/start-message-queues.md) for details.
