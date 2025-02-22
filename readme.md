# Rocketfuel merchant account setup

1. Go to https://stage1.rocketdemo.net/ and register as a Merchant 
2. Confirm your email
3. Connect your e-shop = click Edit in the lower left corner, fill in all the fields, copy your merchantID
4. Type in your shop callback URL (for example, *https://yourshop.com/rocketfuel-callback*)

# Payments (via Rocketfuel extension or popup window/iframe)

After a user creates a new order in your shop and clicks Rocketfuel payment method button on a checkout page, order information should be sent to Rocketfuel

## Payload (cart) proper format

Use [rocketfuel-php-sdk](https://bitbucket.org/rocketfuelblockchain/rocketfuel-php-sdk/)

Create a controller method, for example

    <?php
    require_once(dirname(__FILE__) . '/rocketfuel-php-sdk/RocketFuel.php');

    $rocketfuel = new RocketFuel(YOUR_MERCHANT_ID, YOUR_PUBLIC_KEY);

    $result = $rocketfuel->getOrderPayload(ORDER_ID, YOUR_CART, ORDER_TOTAL_PRICE);
    echo $result;

where

- ORDER_ID - order ID should be unique for your shop
- ORDER_TOTAL_PRICE - total price of whole order for payment, in USD only
- YOUR_CART - array looks like
- YOUR_PUBLIC_KEY key should be in PEM format, copy it from your RocketFuel merchant account


    [
        [
            'product_id' => 1,
            'total_price' => 1,22,
            'product_name' => 'NAME OF THE PRODUCT'
            'quantity' => 5
        ],
        ...
        [
            'product_id' => 2,
            'total_price' => 1,32,
            'product_name' => 'NAME OF THE PRODUCT 2'
            'quantity' => 1
        ]
    ]

for example it's possible to get order payload (cart) from shop backend via ajax or something similar, 
next it's needed to use [rocketfuel-js-helper](https://bitbucket.org/rocketfuelblockchain/rocketfuel-js-helper/src/master/) 
on frontend and send cart info to the Rocketfuel popup/iframe or extension

# JS Helper manual

## Install

    npm install
    npm run build

## Usage
- Build script
- Add script to your html

## Basic usage

    const { sendCart } = rocketfuelJsHelper;
    const result = sendCart(cart, iFrameUrl); // if you do not send the cart and url, it will return an error

For stage1 enironment iFrameUrl is https://iframe-stage1.rocketdemo.net/

## possible result values:
1. { type: 'extension', message: 'cart sent to extension' }
2. { type: 'iframe', massage: 'cart sent to iframe' }
3. { type: 'iframe', message: 'iframe closed' }


After a payment is processed Rocketfuel sends data (payload) to a shop callback URL. It's needed to validate the data by
using [rocketfuel-php-sdk](https://bitbucket.org/rocketfuelblockchain/rocketfuel-php-sdk/)

# Using PHP SDK for callbacks


Usage php-sdk example/ it contains callback for check signature for payload. In callback method we use as example
this:

     <?php
    require_once(dirname(__FILE__) . '/rocketfuel-php-sdk/RocketFuel.php');

    $rocketfuel = new RocketFuel(YOUR_MERCHANT_ID, YOUR_PUBLIC_KEY);

    $result = $rocketfuel->callback(JSON_PAYLOAD_FROMROCKETFUEL, 'SIGNATURE');
    echo $result;

where

- JSON_PAYLOAD_FROMROCKETFUEL - json payload, for example:

  {
  "amount": "36.000000",
  "cart": [
  {
  "id": "3", - id product from your shop
  "name": "The best is yet to come' Framed poster - Dimension : 40x60cm", - name of product
  "price": "29.000000" - price of product }
  ],
  "merchant_id": "b49e76e5-34a4-474e-9ab5-dad303f98891",
  "order": "13" - order }

- SIGNATURE - base64 string from payload, for example:

  vh+irAjWa/2HkYalb2IpdKNyrGAJ+4reQVfRa8I4VyM3xhZ6+W4Cv7iixW2GAzyj59RQhroWBLfiS+HM2BpGl9PFlExiZCswTSvela8Rk0F2ghLJHW+hPCeWlIxDkXxA4ldBYHZsP7BzGrvdUubNjm83cmlj2ccD22mpsrMLS+yswCZAdDuvGC7eHuzNlwwt8JbTjx5Jymo3B3bbVKJoWYIZdP9AkLLaF7z7pq4L0PWwKrgJEpRcy5kHiJPsDJg8UKecth4I0ZhxHviC4a2/6zwuZOhbTGKCnojr3WVk+s1rt0F0ZBYlQEbssjCGRwqT+7ZWRCJX7FIemNIg8gxOmA==

callback just checks a payload signature and returns result as a json file:

    [ 'success' => true, 'message' => 'signature valid' ] 

or (if error)

    ['error' => true,'message' => 'signature not valid']

