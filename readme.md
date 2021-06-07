Send and Receive Messages with PHP and RabbitMQ
===============================================

* ***Actions on the deployment of the project:***

- Making a new project `rabbitmq_pwspirit.loc`:
																	
sudo chmod -R 777 /var/www/LARAVEL/RabbitMQ/rabbitmq_pwspirit.loc

//!!!! .conf
sudo cp /etc/apache2/sites-available/test.loc.conf /etc/apache2/sites-available/rabbitmq_pwspirit.loc.conf
		
sudo nano /etc/apache2/sites-available/rabbitmq_pwspirit.loc.conf

sudo a2ensite rabbitmq_pwspirit.loc.conf

sudo systemctl restart apache2

sudo nano /etc/hosts

cd /var/www/LARAVEL/RabbitMQ/rabbitmq_pwspirit.loc

- Deploy project:

	`git clone << >>`
	
	_+ Сut the contents of the folder up one level and delete the empty one._

	`composer install`		

---

* ***Install RabbitMQ***

<https://coderun.ru/blog/kak-ustanovit-rabbitmq-server-v-ubuntu-18-04-i-16-04-lts/>

	echo 'deb http://www.rabbitmq.com/debian/ testing main' | sudo tee /etc/apt/sources.list.d/rabbitmq.list
	wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -

	sudo apt-get update
	sudo apt-get install rabbitmq-server

	sudo systemctl enable rabbitmq-server
	sudo systemctl start rabbitmq-server
	sudo systemctl stop rabbitmq-server

	sudo systemctl status rabbitmq-server

	sudo rabbitmq-plugins enable rabbitmq_management
	
---

Programming With Spirit

[Send and Receive Messages with PHP and RabbitMQ (11:00)]( https://www.youtube.com/watch?v=bj4GFsv3_Yc&ab_channel=ProgrammingWithSpirit )

Demonstration on using PHP client to send and receive RabbitMq messages.

- ALLOCATE MEMORY:

	`free -m`
	
	`sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024`
	
	`sudo /sbin/mkswap /var/swap.1`
	
	`sudo /sbin/swapon /var/swap.1`

Error: 
	
_"proc_open(): fork failed - Cannot allocate memory"_	

<https://www.nicesnippets.com/blog/proc-open-fork-failed-cannot-allocate-memory-laravel-ubuntu>

	cd /var/www/LARAVEL/RabbitMQ/rabbitmq_pwspirit.loc

	composer require php-amqplib/php-amqplib
	
		- Install the latest version.

OR

[(1:05)]( https://youtu.be/bj4GFsv3_Yc?t=65 ) `composer.json` + `composer update`:

```
{
    "require": {
        "php-amqplib/php-amqplib": "^3.0"
    }
}
```

[(3:30)]( https://youtu.be/bj4GFsv3_Yc?t=210 ) `send.php`:

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->queue_declare('Php queue', false, false, false, false);

$msg = new AMQPMessage('Hello World!');
$channel->basic_publish($msg, '', 'Php queue');

echo " [x] Sent 'Hello World!'\n";

$channel->close();
$connection->close();
?>
```

[(9:00)]( https://youtu.be/bj4GFsv3_Yc?t=540 ) `receive.php`:

```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
use PhpAmqpLib\Connection\AMQPStreamConnection;

$connection = new AMQPStreamConnection('localhost', 5672, 'guest', 'guest');
$channel = $connection->channel();

$channel->queue_declare('Php queue', false, false, false, false);

echo " [*] Waiting for messages. To exit press CTRL+C\n";

$callback = function ($msg) {
    echo ' [x] Received ', $msg->body, "\n";
};

$channel->basic_consume('Php queue', '', false, true, false, false, $callback);

while ($channel->is_consuming()) {
    $channel->wait();
}

$channel->close();
$connection->close();
?>
```

[(9:45)]( https://youtu.be/bj4GFsv3_Yc?t=585 ) `In Terminal`:

	cd /var/www/LARAVEL/RabbitMQ/rabbitmq_pwspirit.loc

	php send.php
	
![screenshot of sample]( https://github.com/mslobodyanyuk/rabbitmq_pwspirit/blob/master/public/images/1.png )
		
[(9:55)]( https://youtu.be/bj4GFsv3_Yc?t=595 ) `RabbitMQ Manager` - `In Browser`:
					
	127.0.0.1:15672	
	
OR

	http://rabbitmq_pwspirit.loc:15672/
							
	guest	
	guest					
										
![screenshot of sample]( https://github.com/mslobodyanyuk/rabbitmq_pwspirit/blob/master/public/images/2.png )

[(10:10)]( https://youtu.be/bj4GFsv3_Yc?t=610 )

![screenshot of sample]( https://github.com/mslobodyanyuk/rabbitmq_pwspirit/blob/master/public/images/3.png )

[(10:30)]( https://youtu.be/bj4GFsv3_Yc?t=630 )	`In another Terminal`:

	cd /var/www/LARAVEL/RabbitMQ/rabbitmq_pwspirit.loc

	php receive.php

![screenshot of sample]( https://github.com/mslobodyanyuk/rabbitmq_pwspirit/blob/master/public/images/4.png )

[(10:45)]( https://youtu.be/bj4GFsv3_Yc?t=645 ) If we go back to console( - `RabbitMQ Manager` ), there is no messages:

![screenshot of sample]( https://github.com/mslobodyanyuk/rabbitmq_pwspirit/blob/master/public/images/5.png )

#### Useful links:

Programming With Spirit

[Send and Receive Messages with PHP and RabbitMQ]( https://www.youtube.com/watch?v=bj4GFsv3_Yc&ab_channel=ProgrammingWithSpirit )

<https://github.com/ProgrammingWithSpirit/RabbitMqMessagingPHP>

---

Install RabbitMQ

<https://coderun.ru/blog/kak-ustanovit-rabbitmq-server-v-ubuntu-18-04-i-16-04-lts/>

[Install RabbitMQ on Ubuntu and work with NodeJS]( https://www.youtube.com/watch?v=FmAMhpeek8A&ab_channel=NevyanNeykov )

---

<https://www.cloudamqp.com/blog/how-to-run-rabbitmq-with-php.html>

Possible Errors

<https://www.nicesnippets.com/blog/proc-open-fork-failed-cannot-allocate-memory-laravel-ubuntu>