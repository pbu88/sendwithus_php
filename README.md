sendwithus_php
==============

sendwithus PHP Client

## status
[![Build Status](https://travis-ci.org/sendwithus/sendwithus_php.png)](https://travis-ci.org/sendwithus/sendwithus_php)

## requirements
    curl library must be installed and enabled in php.ini

Install it via Composer
-----------------------

Add it to your composer.json
```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/sendwithus/sendwithus_php"
        }
    ],
    "require": {
        "sendwithus/api": "dev-master"
    }
}
```
Then install it with

    composer install


## Getting started

```php
// Yii Users
Yii::$classMap = array(
    'sendwithus\\API' => dirname($_SERVER['DOCUMENT_ROOT']) . '/path/to/sendwithus/lib/API.php'
);

// composer users
use sendwithus\API;

require_once 'vendor/autoload.php';


$API_KEY = 'THIS_IS_A_TEST_API_KEY';
$options = array(
    'DEBUG' => true
);

$api = new API($API_KEY, $options);
```

# Emails

## Get emails

```php
$response = $api->emails();
```

## Create emails

_We validate all HTML content_
```php
$response = $api->create_email('Email Name',
    'Email Subject',
    '<html><head></head><body>Valid HTML<body></html>',
    'Optional text content')
```

## Send emails
```php
// Send function header
send(
    $email_id,      // string, id of email to send
    $recipient,     // array, ("address", "name") to send to
    $options        // (optional) array, (array) additional parameters - (see below)
)

// Send function options
'email_data'  // array of variables to merge into the template.
'sender'      // array ("address", "name", "reply_to") of sender.
'cc'          // array of ("address", "name") for carbon copy.
'bcc'         // array of ("address", "name") for blind carbon copy.
'inline'      // string, path to file to include inline.
'files'       // array, paths to files to attach to the send.
'tags'        // array of strings to tag email send with.
'esp_account' // string of ESP ID to manually select ESP
```

## Send Examples

### Send request with REQUIRED parameters only

```php
$response = $api->send('email_id',
    array('address' => 'us@sendwithus.com')
);
```

### Send request with REQUIRED and OPTIONAL parameters

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'email_data' => array('name' => 'Jimmy the snake'),
        'sender' => array(
            'name' => 'Company',
            'address' => 'company@company.com',
            'reply_to' => 'info@company.com'
			)
		'esp_account' => 'esp_EMpi5eo59cG4cCWd7AdW7J'
    )
);
```

### Send an email with multiple CC/BCC recipients

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'email_data' => array('name' => 'Jimmy the snake'),
        'sender' => array(
            'name' => 'Company',
            'address' => 'company@company.com',
            'reply_to' => 'info@company.com'
        ),
        'cc' => array(
            array(
                'name' => 'CC Name',
                'address' => 'CC@company.com'
            ),
            array(
                'name' => 'CC 2 Name',
                'address' => 'CC2@company.com'
            )
        ),
        'bcc' => array(
            array(
                'name' => 'BCC Name',
                'address' => 'BCC@company.com'
            )
        )
    )
);
```

### Send an email with a dynamic tag

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'tags' => array('Production', 'Client1')
    )
);
```

### Send specific version of an email

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'version_name' => 'My Version'
    )
);
```

### Send email with an inline image attachment

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'inline' => 'filename.jpg'
    )
);
```

### Send email with attachments

```php
$response = $api->send('email_id',
    array(
        'name' => 'Matt',
        'address' => 'us@sendwithus.com'),
    array(
        'files' => array(
            'filename.txt',
            'filename.pdf'
        )
    )
);
```

## Send to a Segment
```php
send_segment(
    $email_id,          // id of template to send
    $segment_id,        // id of the segment to send to
    $data               // optional array of data to send
)
```

Example

```php
$response = $api->send_segment('us@sendwithus.com', 'segment_1234');
```

## Drip Unsubscribe
```php
// Unsubscribe email address from active drips
drip_unsubscribe(
    $email_address,      // the email to unsubscribe from active drips
)
```

## Drip Unsubscribe Example

```php
$response = $api->drip_unsubscribe('us@sendwithus.com');
```

## Customers API

### Create Customer
```php
create_customer(
    $email,             // string, email of customer
    $data,              // array, optional, data for customer
)
```

Example

```php
$response = $api->create_customer('us@sendwithus.com',
    array('name' => 'Sendwithus')
);
```

### Update Customer
```php
update_customer(
    $email,             // string, email of customer
    $data,              // array, optional, data for customer
)
```

Example

```php
$response = $api->update_customer('us@sendwithus.com',
    array('name' => 'Sendwithus.com')
);
```

### Delete Customer
```php
delete_customer(
    $email,             // string, email of customer
)
```

Example

```php
$response = $api->delete_customer('us@sendwithus.com');
```


## Expected response

### Success
```php
print $response->success; // true

print $response->status; // "OK"

print $response->receipt_id; // ### numeric receipt_id you can use to query email status later
```

### Error cases
```php
print $response->success; // false

print $response->status; // "error"

print $response->exception; // Exception Object

print $response->code;
// 400 (malformed request)
// 403 (bad api key)
```

