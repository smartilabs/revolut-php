# RevolutPHP

> (Unofficial) PHP API Client and bindings for the [Revolut Business API](https://revolut-engineering.github.io/api-docs/).

[![Build Status](https://travis-ci.org/smartilabs/revolut-php.svg?branch=master)](https://travis-ci.org/sverraest/revolut-php)
[![codecov](https://codecov.io/gh/smartilabs/revolut-php/branch/master/graph/badge.svg)](https://codecov.io/gh/sverraest/revolut-php)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/smartilabs/revolut-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/smartilabs/revolut-php/?branch=master)
[![Maintainability](https://api.codeclimate.com/v1/badges/c24e78a43d1134aaf1b4/maintainability)](https://codeclimate.com/github/smartilabs/revolut-php/maintainability)
[![Latest Stable Version](https://poser.pugx.org/sverraest/revolut-php/v/stable)](https://packagist.org/packages/smartilabs/revolut-php)
[![License](https://poser.pugx.org/sverraest/revolut-php/license)](https://packagist.org/packages/smartilabs/revolut-php)
[![composer.lock](https://poser.pugx.org/sverraest/revolut-php/composerlock)](https://packagist.org/packages/smartilabs/revolut-php)

Using this PHP API Client you can interact with your:
- 💰 __Accounts__
- 🏢 __Counterparties__
- 💸 __Payments__
- 🕰️ __Payment Drafts__
- 🔀 __Transfers__ 
- 📊 __Transactions__
- 💹 __Rates__
- 💱 __Exchanges__
- 🔗 __Webhooks__


## Installation

Requires PHP 7.0 or higher

The recommended way to install revolut-php is through [Composer](https://getcomposer.org):

First, install Composer:

```
$ curl -sS https://getcomposer.org/installer | php
```

Next, install the latest revolut-php:

```
$ php composer.phar require sverraest/revolut-php
```

Finally, you need to require the library in your PHP application:

```php
require "vendor/autoload.php";
```

## Development

- Run `composer test` and `composer phpcs` before creating a PR to detect any obvious issues.
- Please create issues for this specific API Binding under the [issues](https://github.com/sverraest/revolut-php/issues) section.
- [Contact Revolut](https://business.revolut.com/signin) directly for official Revolut For Business API support.


## Quick Start
### RevolutPHP\Auth\Provider
Start by following the authentication instructions in the [Revolut API docs](https://revolut-engineering.github.io/api-docs/#business-api-authentication):
```
openssl genrsa -out privatekey.pem 1024
openssl req -new -x509 -key privatekey.pem -out publickey.cer -days 1825
```

Paste the generated public key on the Revolut for Business API settings page, and use the private key to instantiate a new `RevolutPHP\Auth\Provider`:
```
$authProvider = new \RevolutPHP\Auth\Provider([
    'clientId' => '{clientId}', // As shown when uploading your key
    'privateKey' => 'file://{privateKeyPath}',
    'redirectUri' => 'https://example.com', // The URL to redirect the user to after the authorisation step 
    'isSandbox' => true
]);
```
You can now redirect the user to the authorisation flow in the Revolut for Business app:
```
$url = $authProvider->getAuthorizationUrl();
```

Once the user has confirmed authorisation, the user will be redirected back to the redirectUri with an authorisation code attached. You can exchange this authorisation code for an access token:
```
$accessToken = $authProvider->getAccessToken('authorization_code', [
    'code' => $_GET['code']
]);
```

You can save this `$accessToken` somewhere safe and pass it directly to the RevolutPHP\Client. The token is valid for 40 minutes. To request a new token after expiration, you can use the refresh token to get a new access token:
```
if( $accessToken->hasExpired() ) {
    $newAccessToken = $authProvider->getAccessToken('refresh_token', [
        'refresh_token' => $accessToken->getRefreshToken()
    ]);
}
```

### RevolutPHP\Client
If you want to get a `production` client:

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
```

If you want to get a `sandbox` client:

```php
use RevolutPHP\Client;

$client = new Client($accessToken, 'sandbox');
```

If you want to pass additional [GuzzleHTTP](https://github.com/guzzle/guzzle) options:

```php
use RevolutPHP\Client;

$options = ['headers' => ['foo' => 'bar']];
$client = new Client($accessToken, 'sandbox', $options);
```

## Available API Operations

The following exposed API operations from the Revolut For Business API are available using the API Client.

See below for more details about each resource.

💰 __Accounts__

Get all accounts, Get a specific account and get details for a specific account.

🏢 __Counterparties__

Get all counterparties, get a specific counterparty, create a new counterparty and delete a counterparty.

💸 __Payments__

Create and schedule new payments.

🕰️ __Payment Drafts__

Create, get and delete payment drafts for approval by a business owner/admin.

🔀 __Transfers__

Create a transfer between your accounts.

📊  __Transactions__

Get all transactions or a subset (with queryFilters), cancel a scheduled transaction, get a specific transaction and get a transaction by the unique specified requestId.

A Transaction is either created as a Payment or a Transfer.

💹  __Rates__

Get exchange rates.


💱  __Exchanges__

There are two ways of using this endpoint:

If you know the amount of currency you want to sell (e.g: I want to exchange 135.5 USD to EUR), then you should specify the amount in the "from" object.

If, on the other hand, you want to specify the amount of currency you want to buy (e.g: I want to exchange USD to receive 200 EUR), then you should specify the amount in the "to" object.

❗ Please note that the "amount" field can be specified only once, either in the "from" object or in the "to" object.


🔗 __Webhooks__

Create new webhooks.

## Usage details

### 💰 Accounts
#### Get all accounts
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-accounts](https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-accounts)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$accounts = $client->accounts->all();
```

#### Get one account
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-account](https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-account)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$account = $client->accounts->get('foo');
```

#### Get account details
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-account-details](https://revolut-engineering.github.io/api-docs/#business-api-business-api-accounts-get-account-details)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$account = $client->accounts->getDetails('foo');
```

### 🏢 Counterparties
#### Add a Counterparty
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-add-revolut-counterparty](https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-add-revolut-counterparty)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$counterparty = $client->counterparties->create(['profile_type' => 'business', 'name' => 'TestCorp' , 'email' => 'test@sandboxcorp.com']);
```

#### Delete a Counterparty
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-delete-counterparty](https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-delete-counterparty)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$client->counterparties->delete('foo');
```

#### Get all Counterparties
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-get-counterparties](https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-get-counterparties)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$counterparties = $client->counterparties->all();
```

#### Get a specific Counterparty
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-get-counterparty](https://revolut-engineering.github.io/api-docs/#business-api-business-api-counterparties-get-counterparty)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$counterparty = $client->counterparties->get('bar');
```

### 💸 Payments
#### Create a payment
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-create-payment](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-create-payment)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$payment = [
  'request_id' => 'e0cbf84637264ee082a848b',
  'account_id' => 'bdab1c20-8d8c-430d-b967-87ac01af060c',
  'receiver' => [
    'counterparty_id': '5138z40d1-05bb-49c0-b130-75e8cf2f7693',
    'account_id': 'db7c73d3-b0df-4e0e-8a9a-f42aa99f52ab'
  ],
  'amount' => 123.11,
  'currency' => 'EUR',
  'reference' => 'Invoice payment #123'
];

$payment = $client->payments->create($payment);
```

#### Schedule a payment (for up to 30 days in the future)
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-schedule-payment](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-schedule-payment)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$payment = [
  'request_id' => 'e0cbf84637264ee082a848b',
  'account_id' => 'bdab1c20-8d8c-430d-b967-87ac01af060c',
  'receiver' => [
    'counterparty_id': '5138z40d1-05bb-49c0-b130-75e8cf2f7693',
    'account_id': 'db7c73d3-b0df-4e0e-8a9a-f42aa99f52ab'
  ],
  'amount' => 123.11,
  'currency' => 'EUR',
  'reference' => 'Invoice payment #123',
  'schedule_for' => '2018-04-20',
];

$payment = $client->payments->create($payment);
```

### 🕰️ Payment Drafts
#### Get all Payment Drafts
See more at [https://revolut-engineering.github.io/api-docs/#business-api-get-payment-drafts](https://revolut-engineering.github.io/api-docs/#business-api-get-payment-drafts)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$paymentDrafts = $client->paymentDrafts->all();
```

#### Get a specific Payment Draft
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-get-payment-drafts-get-payment-draft-by-id](https://revolut-engineering.github.io/api-docs/#business-api-business-api-get-payment-drafts-get-payment-draft-by-id)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$counterparty = $client->paymentDrafts->get('bar');
```

#### Create a Payment Draft
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payment-drafts-create-a-payment-draft](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payment-drafts-create-a-payment-draft)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$draft = [
  'title' => 'Title of payment',
  'schedule_for' => '2017-10-10',
  'payments' => [[
    'currency' => 'EUR',
    'amount' => 123,
    'account_id' => 'db7c73d3-b0df-4e0e-8a9a-f42aa99f52ab',
    'receiver' => [
      'counterparty_id' => '5138z40d1-05bb-49c0-b130-75e8cf2f7693',
      'account_id' => 'bdab1c20-8d8c-430d-b967-87ac01af060c'
    ],
    'reference' => 'External transfer'
  ]]
];

$draft = $client->paymentDrafts->create($draft);
```

#### Delete a payment draft
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-get-payment-drafts-delete-payment-draft](https://revolut-engineering.github.io/api-docs/#business-api-business-api-get-payment-drafts-delete-payment-draft)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$client->paymentDrafts->delete('bar');
```

### 🔀 Transfers
#### Transfer money between your accounts
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-transfers-create-transfer](https://revolut-engineering.github.io/api-docs/#business-api-business-api-transfers-create-transfer)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$transfer = [
  'request_id' => 'e0cbf84637264ee082a848b',
  'source_account_id' => 'bdab1c20-8d8c-430d-b967-87ac01af060c',
  'target_account_id' => '5138z40d1-05bb-49c0-b130-75e8cf2f7693',
  'amount' => 123.11,
  'currency' => 'EUR',
  'description' => 'Expenses funding'
];

$transfer = $client->transfers->create($transfer);
```

### 📊 Transactions
#### Get a specific transaction (Transfer, Payment)
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transaction](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transaction)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$transaction = $client->transactions->get('foo');
```

#### Get a specific transaction by requestId (Transfer, Payment)
You can fetch a transaction by the requestId that you specified on creation.
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transaction](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transaction)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$transaction = $client->transactions->getByRequestId('inv-123456789');
```

#### Cancel a scheduled transaction
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-cancel-payment](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-cancel-payment)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$client->transactions->cancel('foo');
```

#### Get all transactions
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transactions](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transactions)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);
$transactions = $client->transactions->all();
```

#### Get all transactions with filters applied
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transactions](https://revolut-engineering.github.io/api-docs/#business-api-business-api-payments-get-transactions)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$searchFilters = [
  'from' => '2018-01-01', 
  'to' => '2018-04-01', 
  'count' => 50, 
  'counterparty' => 'foo', 
  'type' => 'transfer'
];

$transactions = $client->transactions->all($searchFilters);
```

### 💹 Rates
#### Get exchange rates
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-exchanges-get-exchange-rates](https://revolut-engineering.github.io/api-docs/#business-api-business-api-exchanges-get-exchange-rates)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$rates = $client->rates->get('USD', 'EUR', 100);
```

### 💱 Exchanges
#### Exchange currency
See more at [https://revolut-engineering.github.io/api-docs/#business-api-business-api-exchanges-exchange-currency](https://revolut-engineering.github.io/api-docs/#business-api-business-api-exchanges-exchange-currency)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$exchange = [
  'from' => [
    'account_id' => '7998c061-115a-4779-b7c5-7175c6502ea0',
    'currency' => 'USD',
    'amount' => 135.5
  ],
  'to' => [
    'account_id' => '35ba695a-9153-4f68-ac16-b32f228265c9',
    'currency' => 'EUR'
  ],
  'reference' => 'Time to sell',
  'request_id' => 'e0cbf84637264ee082a848b'
];

$response = $client->exchanges->exchange($exchange);
```

### 🔗 Webhooks
#### Create a webhook
See more at [https://revolut-engineering.github.io/api-docs/#business-api-web-hooks](https://revolut-engineering.github.io/api-docs/#business-api-web-hooks)

```php
use RevolutPHP\Client;

$client = new Client($accessToken);

$webhook = [
  'url' => 'https://foo.bar',
];

$webhook = $client->webhooks->create($webhook);
```

## Frameworks

If you're looking to use this PHP API Client in a specific PHP framework you currently have the following options:

| Framework                  | Package                                                        |
| ---------------------------|----------------------------------------------------------------| 
| Symfony                    | https://github.com/sverraest/revolut-php-bundle                |
| Laravel                    | https://github.com/vdbelt/laravel-revolut                      |


## Errors
Currently the following errors are defined in the Revolut Business API.

| Error                       | Description                                                        |
| --------------------------- |--------------------------------------------------------------------| 
| 400	Bad request           | Your request is invalid.                                             |
| 401	Unauthorized          | Your API key is wrong.                                               |
| 403	Forbidden             | Access to the requested resource or action is forbidden.             |
| 404	Not Found             | The requested resource could not be found.                           |
| 405	Method Not Allowed    | You tried to access an endpoint with an invalid method.              |
| 406	Not Acceptable        | You requested a format that isn't JSON.                              |
| 429	Too Many Requests     | You're sending too many requests.                                    |
| 500	Internal Server Error | We had a problem with our server. Try again later.                   |
| 503	Service Unavailable   | We're temporarily offline for maintenance. Please try again later.   | 

## About

You can follow me on 🐦 [Twitter](https://www.twitter.com/simondoestech) or ✉️ email me at simon[-at-]appfleet.uk.

---
www.appfleet.uk

<img src="https://avatars3.githubusercontent.com/u/25485341?s=200&v=4" width="50"></img>
