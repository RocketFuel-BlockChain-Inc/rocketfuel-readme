#Setup rocketfel merchant account

1. при регистрации выбрать Merchant
2. регистрация и подтверждение почты
3. connect your store - 
    copy your merchantID 
    set shop url
    set callback, for example *https://yourshop.example/callback-rocketfuel*


After you create new order you should send it to rocketfuel, need use [rocketfuel-js-sdk](https://bitbucket.org/rocketfuelblockchain/rocketfuel-js-sdk/)
#Using js sdk


Rocketfuel, after making payment send data to your callback-url
Need check by it using [rocketfuel-php-sdk](https://bitbucket.org/rocketfuelblockchain/rocketfuel-php-sdk/)
#Using php sdk

install php sdk 

##via composer 

    composer require rocketfuel/rocketfuel-php-sdk @dev

##or git 

    git clone https://bitbucket.org/rocketfuelblockchain/rocketfuel-php-sdk.git

    cd rocketfuel-php-sdk

    composer install

Usage php-sdk example/ it contains only callback for check signature for payload. In callback method we use as example this:


     <?php
    require_once(dirname(__FILE__) . '/rocketfuel-php-sdk/RocketFuel.php');

    $rocketfuel = new RocketFuel(YOUR_MERCHANT_ID);

    $result = $rocketfuel->callback(JSON_PAYLOAD_FROMROCKETFUEL, 'SIGNATURE');
    echo $result;

where 

*JSON_PAYLOAD_FROMROCKETFUEL* - json payload, for example:
{
        "amount": "36.000000",
        "cart": [
            {
                "id": "3", - id product from your shop
                "name": "The best is yet to come' Framed poster - Dimension : 40x60cm", - name of product
                "price": "29.000000" - price of product
            }
        ],
        "merchant_id": "b49e76e5-34a4-474e-9ab5-dad303f98891", 
        "order": "13" - order
}

-SIGNATURE - base64 string from payload. for example:
"vh+irAjWa/2HkYalb2IpdKNyrGAJ+4reQVfRa8I4VyM3xhZ6+W4Cv7iixW2GAzyj59RQhroWBLfiS+HM2BpGl9PFlExiZCswTSvela8Rk0F2ghLJHW+hPCeWlIxDkXxA4ldBYHZsP7BzGrvdUubNjm83cmlj2ccD22mpsrMLS+yswCZAdDuvGC7eHuzNlwwt8JbTjx5Jymo3B3bbVKJoWYIZdP9AkLLaF7z7pq4L0PWwKrgJEpRcy5kHiJPsDJg8UKecth4I0ZhxHviC4a2/6zwuZOhbTGKCnojr3WVk+s1rt0F0ZBYlQEbssjCGRwqT+7ZWRCJX7FIemNIg8gxOmA=="


callback just check signature, and return result json, looks like:
[ 'success' => true, 'message' => 'signature valid' ] or if error ['error' => true,'message' => 'signature not valid']

