# Rocketfuel Webhook and Events

RocketFuel webhook call are triggered to update the merchant on the nupdated status of the transaction. 

Rocketfuel webhooks support following events:-

* Transaction is marked as pending / initiated
* Transaction is marked as partial paid
* Transaction is marked as successful
* Transaction is marked as failed


# Securing Callbacks

Order callbacks originating from RocketFuel will be signed using our callback signing RSA private key.

If you would like to verify callbacks manually in the language of your choice, the message digest used is SHA256, the message that is signed is the POST body, the padding scheme is PKCS1_v1_5, and the signature to be verified is present in the ‘signature’ HTTP data encoded as base64.

## PHP
```php
public function verifyCallback($body, $signature)
{
    $signature_buffer = base64_decode( $signature );
    return (1 == openssl_verify($body, $signature_buffer, self::getCallbackPublicKey(), OPENSSL_ALGO_SHA256));
}
```
## Node.js
```js
function verifySignature(body, public_key_rsa, signature) {
  const verifier = crypto.createVerify('RSA-SHA256');
  verifier.update(body);
  return verifier.verify(public_key_rsa, signature, 'base64');
}
```

## Test data
To assist you in testing, the following is an example POST body with associated valid signature:

### Payload:
```json
{"amount":"24","conversionRate":{"fiatCurrency":"USD","rate":1},"cryptoAmount":"24","cryptoCurrency":"USD","currency":"USD","offerId":"1636959488047","paymentStatus":"1","receivedAmount":"0","referenceId":"346d797e-aa26-4907-b75a-04539ff0a0a8","status":true,"transactionId":"3c56d8fa-32d3-41e6-8563-d5990ffaf7dd"}
```
#### Where
1. amount :-  Amount (Float)
1. currency :- Currency of transaction
2. offerId :- Merchant database Order/offer id
3. referenceId :- RKFL database reference transaction id
4. status:- 
    * true:- successful
    * false:- pending/failed/partial
5. paymentStatus
    * 0 - pending
    * 1 - successful
    * -1 - failed
    * 101 - partial
6. cryptoAmount :- Crypto value of transaction
7. cryptoCurrency :- Crypto currency used
8. receivedAmount :- Received crypto amount
9. conversionRate :- Rate of conversion from fiat to crypto during transaction
    * fiatCurrency - Base currency for conversion
    * rate - conversion rate (multiplier)

### Signature:
```
f09HYBeZFqMkeo/ri5kZI0DGnCiSnYSl2KSZLaB3tIL722a1IsnCSsWsfdZRAiv/7e/MdqguXTBmEUdBzKnzR2ATBJF5VRtLeD7LhnNxpSs1+sAAgIwI2JS6nkRj8DTKZbZUzweGSdgARZfxxoVqQaaW4DPb8kXhGPVo/tOG8Rw62Vbyg279ysgWCtNuYltKg05DFxfWy287LtBnvs3kaw0xoTuR5rCnEncFFLRozSCPRSRU0Ebb3kfWNK6surso9OrqVkdbzXLCpLuLkkakxNvNpahzvB3DuT2zZn0NFxP8YGJquAVcWLh2aj0syRPDArHY5An5CtQ6nuiiJB6jTw==
```

### Public RSA key
```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2e4stIYooUrKHVQmwztC
/l0YktX6uz4bE1iDtA2qu4OaXx+IKkwBWa0hO2mzv6dAoawyzxa2jmN01vrpMkMj
rB+Dxmoq7tRvRTx1hXzZWaKuv37BAYosOIKjom8S8axM1j6zPkX1zpMLE8ys3dUX
FN5Dl/kBfeCTwGRV4PZjP4a+QwgFRzZVVfnpcRI/O6zhfkdlRah8MrAPWYSoGBpG
CPiAjUeHO/4JA5zZ6IdfZuy/DKxbcOlt9H+z14iJwB7eVUByoeCE+Bkw+QE4msKs
aIn4xl9GBoyfDZKajTzL50W/oeoE1UcuvVfaULZ9DWnHOy6idCFH1WbYDxYYIWLi
AQIDAQAB
-----END PUBLIC KEY-----

```