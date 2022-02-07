# QR Code
#### 1. Perform Authentication login (Server to Server Call)

```
curl --location -g --request POST '{{protocol}}://{{host_port}}/api/auth/login' \
--header 'Content-Type: application/json' \
--data-raw '{
    "email": "hari.sharma@yopmail.com", 
    "password": "Hari@ttn1"
}'
```
```
Response:-
{"ok":true,"result":{"access":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImZhMWIxODdkLTNmMWUtNDcxNy04MDU1LWUyMWY4MGE1NjBlYyIsImlhdCI6MTY0NDI1ODI0OSwiZXhwIjoxNjQ0MzQ0NjQ5fQ.xl5_lFaKMzFvfPGxfZqcKxKNPWn4LMGZKMJrxVf7x_Q","refresh":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImZhMWIxODdkLTNmMWUtNDcxNy04MDU1LWUyMWY4MGE1NjBlYyIsImlhdCI6MTY0NDI1ODI0OSwiZXhwIjoxNjQ2ODUwMjQ5fQ.NW-4sR1AtNWnlksGaMUbq_sI6fVfk0dlJ4KmusMUyYU","status":2}}
```


#### 2. Get list of Crypto Currencies 

```
curl --location -g --request GET '{{protocol}}://{{host_port}}/api/currencies' \
--header 'authorization: Bearer {{auth_header}}' \
--data-raw ''
```
```
Response:-
{"ok":true,"result":[{"id":"BTC","fullTitle":"Bitcoin","decimals":8,"currentRate":"44225.09999999","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":1,"isEnabled":true,"isStableCoin":false},{"id":"ETH","fullTitle":"Ethereum","decimals":8,"currentRate":"3157.4","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":2,"isEnabled":true,"isStableCoin":false},{"id":"LTC","fullTitle":"Litecoin","decimals":8,"currentRate":"136.865","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":3,"isEnabled":true,"isStableCoin":false},{"id":"BCH","fullTitle":"Bitcoin Cash","decimals":8,"currentRate":"340.30999999","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":4,"isEnabled":true,"isStableCoin":false},{"id":"XRP","fullTitle":"XRP","decimals":6,"currentRate":"0.79013593","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":5,"isEnabled":true,"isStableCoin":false},{"id":"USDC","fullTitle":"USD Coin","decimals":6,"currentRate":"1","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":6,"isEnabled":true,"isStableCoin":false},{"id":"SILAUSD","fullTitle":"SILAUSD","decimals":18,"currentRate":"NaN","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":7,"isEnabled":true,"isStableCoin":false},{"id":"USD","fullTitle":"US Dollar","decimals":2,"currentRate":"1","symbol":"","isPayment":false,"isFiat":true,"fiatEquivalent":null,"sort":8,"isEnabled":true,"isStableCoin":false},{"id":"EUR","fullTitle":"Euro","decimals":2,"currentRate":"1.1418145","symbol":"","isPayment":false,"isFiat":true,"fiatEquivalent":null,"sort":9,"isEnabled":true,"isStableCoin":false},{"id":"RUB","fullTitle":"Russian ruble","decimals":2,"currentRate":"0.01322403","symbol":"","isPayment":false,"isFiat":true,"fiatEquivalent":null,"sort":10,"isEnabled":true,"isStableCoin":false},{"id":"GALA","fullTitle":"Gala","decimals":8,"currentRate":"0.35979499","symbol":"","isPayment":true,"isFiat":false,"fiatEquivalent":null,"sort":null,"isEnabled":true,"isStableCoin":false}]}
```

#### 3. Generate QR code 
   - receiverAddress value needs to be displayed in the QR code
```
curl --location --request POST '{{protocol}}://{{host_port}}/api/merchant/qr/generate' \
--header 'authorization: Bearer {{auth_header}}' \
--header 'Content-Type: application/json' \
--data-raw '{"clientId":"22227220-3153-4094-bdca-6007afb361ba","amount":"300.00","currency":"BCH","cart":[{"id":"1","name":"Best Buy Gift Card","price":15,"quantity":1}],"merchantStoreCurrency":"USD","offerId":"H907"}'
```
```
Response:-
{"ok":true,"result":{"id":"d3544f03-ecc7-44bb-bba2-96200a0124be","receivedAmount":"0","type":0,"nativeAmount":"300","nativeCurrency":"USD","amount":"0.88070809","localAmount":"300.00","localCurrency":"USD","currency":"BCH","rateDivisor":"340.635","hostedPageId":null,"currencyRateDiff":"0","check":[{"id":"1","num":0,"name":"Best Buy Gift Card","price":15,"quantity":1,"localAmount":15,"statusRefund":{"pending":0,"success":0,"rejected":0}}],"meta":{"offerId":"H907","receiverAddress":"18rj3DHsEV4WaprYVuEhBi2MSS2BLcqRPG"},"stockId":null,"userId":null,"merchantId":"22227220-3153-4094-bdca-6007afb361ba","status":10,"statusRefund":20,"shopperExchangeRate":"0","merchantExchangeRate":"100","refundExchangeRate":"0","shopperExchangeRateValue":"0","merchantExchangeRateValue":"0.176141618","refundExchangeRateValue":"0","shopperFeeAmount":"0","merchantFeeAmount":"60","refundFeeAmount":"0","shopperFeeFlatAmount":"0","merchantFeeFlatAmount":"-240.00","refundFeeFlatAmount":"0","partnerMerchantFeeRate":"0","partnerMerchantFlatRate":"0","partnerMerchantAmount":"0","merchantPayableAmount":"240","rkflCollection":"60","partnerCollection":"0","partnerId":"bf71766b-dd18-48d5-92fe-d764cfdd082b","updatedAt":"2022-02-07T18:19:43.529Z","createdAt":"2022-02-07T18:19:41.800Z","description":null,"incrementMe":4284,"referenceDwolla":null,"errorMessage":""}}
```

#### 4. Validate payment

```
curl '{{protocol}}://{{host_port}}/api/purchase/external-status-qr' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Cache-Control: no-cache' \
  -H 'sec-ch-ua: " Not;A Brand";v="99", "Google Chrome";v="97", "Chromium";v="97"' \
  -H 'Accept: application/json, text/plain, */*' \
  -H 'Content-Type: application/json' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.99 Safari/537.36' \
  -H 'sec-ch-ua-platform: "Linux"' \
  -H 'Origin: http://localhost:3000' \
  -H 'Sec-Fetch-Site: same-site' \
  -H 'Sec-Fetch-Mode: cors' \
  -H 'Sec-Fetch-Dest: empty' \
  -H 'Referer: http://localhost:3000/' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  --data-raw '{"offerId":"TR09","merchantId":"22227220-3153-4094-bdca-6007afb361ba"}' \
  --compressed
  ```
  ```
  Response:
  {"ok":true,"result":{"responseObj":{"txStatus":"pending","recievedAmount":null,"currency":"LTC"}}}
  ```