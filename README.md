### «Твои Платежи» - Платежи для сайтов, приложений, игр и платформ
💖 [Заявка на подключение](https://ypmn.ru/ru/connect/?utm_source=github_index)
📍 [Контакты](https://ypmn.ru/ru/contacts/?utm_source=github_index)
⚙ [Разработчикам](https://ypmn.ru/ru/documentation/)

![](https://ypmn.ru/s/img/ypmn_window-green.png)

Получите Код Интеграции и Секретный Ключ у Вашего менеджера, и интегрируйте платежи с приложением на PHP за 30 секунд с помощью пакета Composer: 
```shell
$ composer require yourpayments/php-api-client
```

```php
<?php

declare(strict_types=1);

require vendor/autoload.php;

/**
 * Создадим объект Мерчанта
 * (получите Интеграционный Код Мерчанта и Секретный Ключ у вашего менеджера YPMN)
 *
 * Теперь включайте этот файл везде, где работаете с платежами
 */
$merchant = new Merchant('CC1', 'SECRET_KEY');

// Установим номер (ID) заказа (номер заказа в вашем магазине, должен быть уникален в вашей системе)
$merchantPaymentReference = "order_id_" . time();

$orderAsProduct = new Product([
    'name'  => 'Заказ №' . $merchantPaymentReference,
    'sku'  => $merchantPaymentReference,
    'unitPrice'  => 200.42,
    'quantity'  => 1,
]);

// Опишем Биллинговую (платёжную) информацию
$billing = new Billing;
// Установим Код страны
$billing->setCountryCode('RU');
// Установим Имя Плательщика
$billing->setFirstName('Иван');
// Установим Фамилия Плательщика
$billing->setLastName('Петров');
// Установим Email Плательщика
$billing->setEmail('test1@ypmn.ru');
// Установим Телефон Плательщика
$billing->setPhone('+7-800-555-35-35');
// Установим Город
$billing->setCity('Москва');

// Создадим клиентское подключение
$client = new Client;
// Установим биллинг
$client->setBilling($billing);

// Создадим платёж
$payment = new Payment;
// Установим позиции
$payment->addProduct($orderAsProduct);
// Установим валюту
$payment->setCurrency('RUB');
// Создадим и установим авторизацию по типу платежа
$payment->setAuthorization(new Authorization('CCVISAMC',true));
// Установим номер заказа (должен быть уникальным в вашей системе)
$payment->setMerchantPaymentReference($merchantPaymentReference);
// Установим адрес перенаправления пользователя после оплаты
$payment->setReturnUrl('https://test.u2go.ru/php-api-client/?function=returnPage');
// Установим клиентское подключение
$payment->setClient($client);

// Создадим HTTP-запрос к API
$apiRequest = new ApiRequest($merchant);
// Включить режим отладки (закомментируйте или удалите в рабочей программе!)
$apiRequest->setDebugMode();
// Переключиться на тестовый сервер (закомментируйте или удалите в рабочей программе!)
$apiRequest->setSandboxMode();
// Отправим запрос
$responseData = $apiRequest->sendAuthRequest($payment, $merchant);
// Преобразуем ответ из JSON в массив
$responseData = json_decode((string) $responseData["response"], true);

// Выведем кнопку оплаты
echo Std::drawYpmnButton([
    'url' => $responseData["paymentResult"]['url'],
    'sum' => $payment->sumProductsAmount(),
]);
```

⚙ [Подробная документация](https://github.com/yourpayments/php-api-client/?utm_source=github_index)
