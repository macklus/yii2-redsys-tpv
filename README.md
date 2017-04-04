Redsys Tpv plugin
===============
Manage payment form generator from Redsys TPV

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist macklus/yii2-redsys-tpv "*"
```

or add

```
"macklus/yii2-redsys-tpv": "*"
```

to the require section of your `composer.json` file.


Configure
---------

Configure tpv component, using:

```php
'redsys' => [
            'class' => 'macklus\RedsysTpv\Tpv',
            'mode' => 'prod',
            'debug' => [
                'Merchant' => '0XXXXXXXX',
                'Terminal' => '0000000X',
                'Key' => 'XXXXXXXX',
                'URL_OK' => '',
                'URL_NOK' => '',
				'Version' => '',
            ],
            'prod' => [
                'Merchant' => '0XXXXXXXX',
                'Terminal' => '0000000X',
                'Key' => 'XXXXXXXX',
                'URL_OK' => '',
                'URL_NOK' => '',
				'Version' => '',
            ],
        ],
```

The **mode** variable define usage mode, so far:

1. **prod:** production mode
2. **debug:** debug mode

Generate the payment form
-------------------------

You can generate the payment form using next code in your view:

```php
<?php

use Yii;
?>

<?= Yii::$app->redsys->generateForm($Num_operacion, $Importe, $TipoMoneda = 978, $idioma = 1, $showButton = true) ?>
```

* **Num_operation:** Alphanumeric code who identifies this order
* **Importe:** The total amount of payment
* **TipoMoneda:** Coin
* **Idioma:** Language
* **showButton:** if true, the form include submit button. If false, you should submit it througt javascript. Remember that form id is always ceca-tpv-form

Handle TPV response
-------------------

Once you have been pay, TPV should POST payment info (if you configure it). To handle that, you should use in your action:

```php
<?php
class PaymentsController extends Controller
{

    public function init()
    {
        if (isset($_POST)) {
            /* Turn off CSRF */
            Yii::$app->request->enableCsrfValidation = false;
        }
    }
    
    public function actionTpv()
    {
        $logfile = Yii::getAlias('@runtime/tpv.log');

        if (isset($_POST)) {
            file_put_contents($logfile, print_R($_POST, true), FILE_APPEND);
        }

        $response = Yii::$app->tpv->getTPVResponse();
        if ($response && $response->isValid()) {
          // do some stuff on your database or app
          if( myStuffWorkFine ) {
            $response->returnOkToServer();
          } else {
            $response->returnErrorToServer();
          }
        }

        Yii::$app->request->enableCsrfValidation = true;
    }
}

```
