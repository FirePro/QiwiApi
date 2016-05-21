# Клиент для работы c QIWI
Класс для работы с интерфейсом QIWI на PHP при помощи Api. Благодаря данному классу, вы можете работать с личным кабинетом QIWI через API, без использования браузера. Написан с использованием стандартов PSR. Архитектура проекта направлена на легкую расширяемость и внедрение нового функционала и использованием SOLID стандартов.

##Требования

* PHP 5.6 
* Composer

##Установка

Установка с использованием менеджера пакетов [Composer](http://getcomposer.org):

Вносим изменения в composer.json:

```json
{ 
    "require": {
        "firepro/qiwiclient": "*"
    }
}
```

Установка через require:

```bash
$ composer require firepro/qiwiclient
```

##Возможности

* Авторизация по логину и паролю
* Запрос истории платежей
* Перевод средств на любой номер QIWI
* Подтверждение платежа через СМС код
* Проверка баланса
* Проверка подключенного СМС-подтверждения
* Работа со счетами
* Покупка/активация Qiwi Eggs

##Пример работы

###Инициализация

Для начала работы необходимо создать экземпляр класса QiwiClient, в который передать по умолчанию $login и $password.

```php
$client = new QiwiClient($login, $password, $provider);
```

По умолчанию, provider - это CurlServiceProvider - он обеспечивает работу клиента через встроенную библиотеку Curl. 

##Работа с запросами

Для начала работы, необходимо понять структуру. Мы используем подход, в котором Client играет роль интерфейса запросов, а обьекты запросов предоставляют все необходимые данные для клиента. Это позволяет идти по лучшим практикам проектирования, не используя антипатерны (God Object)

```php

    $client = new Client(new CurlServiceProvider());

    $request = new QiwiWalletPayRequest($client);
    $request->setAccount("+79030132918");
    $request->setAmount(1);
    $request->setComment(1);
    $request->execute();
    
    /**
    * QiwiPayRequest возвращает QiwiPayResponse
    **/
    $response = $request->getResponse();
    
    echo 'Платеж '. $response->getId()." завершился со статусом ".$response->getState();
```

###Проведение платежей

Для проведения платежей, существуют следующие классы:

* QiwiWalletPayRequest - оплата на кошелек QIWI
* CardPayRequest - оплата на банковскую карту
* YandexMoneyPayRequest - Оплата на кошелек Яндекс.Деньги
* PhonePayRequest - Оплата на счет мобильного телефона
* WebmoneyPayRequest - Оплата на Webmoney

####Проведение оплаты на QIWI кошелек.

```php

    $client = new Client(new CurlServiceProvider());

    $request = new QiwiWalletPay($client);
    $request->setAccount("+79030132918");
    $request->setAmount(1);
    $request->setComment(1);
    $request->execute();
    
    /**
    * QiwiPayRequest возвращает QiwiPayResponse
    **/
    $response = $request->getResponse();
    
    echo 'Платеж '. $response->getId()." завершился со статусом ".$response->getState();
```

####Проведение оплаты на телефон

```php

    $client = new Client(new CurlServiceProvider());

    $request = new QiwiWalletPay($client);
    $request->setAccount("+79030132918");
    $request->setAmount(1);
    $request->setComment(1);
    $request->execute();
    
    /**
    * QiwiPayRequest возвращает QiwiPayResponse
    **/
    $response = $request->getResponse();
    
    echo 'Платеж '. $response->getId()." завершился со статусом ".$response->getState();
```

####Проведение оплаты на банковскую карту

```php

    $client = new Client(new CurlServiceProvider());

    $request = new QiwiWalletPay($client);
    $request->setAccount("+79030132918");
    $request->setAmount(1);
    $request->setComment(1);
    $request->execute();
    
    /**
    * QiwiPayRequest возвращает QiwiPayResponse
    **/
    $response = $request->getResponse();
    
    echo 'Платеж '. $response->getId()." завершился со статусом ".$response->getState();
```

### Подтверждение платежа

```php

    $client = new Client(new CurlServiceProvider());

    $request = new QiwiConfirmPayRequest($client);
    $request->setId("+79030132918");
    $request->setSmsCode(1);
    $request->execute();
    
    /**
    * QiwiPayRequest возвращает QiwiConfirmResponse
    **/
    $response = $request->getResponse();
    
    echo 'Платеж '. $response->getId()." завершился со статусом ".$response->getState();
```

##История и поиск платежа

Для выполнения поиска и авторизации предусмотрены 


###Получение истории платежей

История платежей возвращается итератором экземпляром класса QiwiHistory, перебор которого возвращает обьект QiwiInvoice. Доступные свойства которого:

* $id
* $status
* $text
* $date
* $tiem
* $provider
* $datetime
* $comment
* $cash

###Получение платежа по комментарию

Для того, чтобы найти платеж по комментарию, вызовите метод findInvoice и передайте в него комментарий, по которому нужно найти платеж. Производится получение первого доступного платежа с данным комментарием.

```php 
$client->findInvoice($comment);
```

Возвращаемый обьект Invoice, который содержит в себе всю требуемую информацию о платеже.

###Получение баланса 

Для получения баланса, необходимо вызвать метод getBalance()

```
$client->getBalance();
```

Вам вернется обьект QiwiBalance, который содержит в себе свойства:

* rub
* eur

###Покупка ваучеров

С помощью данного клиента можно удобно работать с QIWI EGGS, для того, чтобы купить ваучер, 

$client->buyEgg();

###Активация ваучеров

$client->activeEgg();

###Создать счет на оплату

$client->createInvoice();

###Cписок входящих счетов

$client->listInvoices();

###Оплата входящего счета

$client->payInvoice();

##Экземпляры работы

###Перевод средств на другой кошелек

```PHP
<?php

$client = new QiwiClient("+7903000000", "test");
$client->authorize();

$request = new QiwiWalletPay("+79030172919");
$request->setAmount(1);
$request->setCurrency("RUB");
$request->setComment("test invoice");

//Вы можете подключить EventListener, наследуемый от интерфейса Firepro/Qiwi/PayEventListener 
//$request->setEventListener(new MyEventListener())

$response = $client->pay($request);

switch ($response->getState()) {

QiwiPayStates::NEED_SMS: 

    $smsCode = "Здесь полученный SMS код";
    $confirmResponse = $client->confirmPay($response->getId(), $smsCode);
    
    if ($confirmResponse->getState()==QiwiPayStates::DONE) {
        echo "Платеж успещно завершен";
    } else {
        var_dump($confirmResponse->getErrors()):
    }
    
    break;
    
QiwiPayStates::DONE:
    
    echo 'Платеж '.$response->getId().' успешно завершен'; 
    
    break;

QiwiPayStates::FAILED:
    echo "Произошли ошибки при выполнении платежа!";
    var_dump($response->getErrors()):
}

?>
```

##Поддержка и контрибьюция

Вы можете обратиться на support@funser.ru для получения помощи по данному клиенту. Также можете вносить изменения через Pull Requests.


