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
{"amount":"0.31","currency":"USD","offerId":"1135","referenceId":"5f29fc82-8f8c-4a07-8b87-4dec1ed3415f","status":true,"transactionId":"b4c47d98-dc45-463b-9790-4222ff38d3e6", "paymentStatus":0,"cryptoAmount":0.2214221,"cryptoCurrency":"BTC","receivedAmount":0.0021342}
```
#### Where
1. amount :-  Amount (Float)
1. currency :- Currency of transaction
1. offerId :- Merchant database Order/offer id
1. referenceId :- RKFL database reference transaction id
1. status:- 
    * true:- successful
    * false:- pending/failed/partial
1. paymentStatus
    * 0 - pending
    * 1 - successful
    * -1 - failed
    * 101 - partial
1. cryptoAmount :- Crypto value of transaction
1. cryptoCurrency :- Crypto currency used
1. receivedAmount :- Received crypto amount

### Signature:
```
dY5argYpL8mFeAp/AjZ3zOfkl46KnR1O+97LcQVloZ9+ED34w+td9WZ7CS5OKC08wkXy4s/6q1tbjaXweXBru9J0ExkpFzyNkWgLYkKSHauRf0uLwV+FVGTZz+1CHa9sHvLrwRlcePw57kF4hl9zdY9AucKHPu5sE5607M1OoQ/PBkxT/mEbzhKgxxRV/GE0BeMol1Ql+94byj1A1wOJYBQkvGiGlxWn8bkHPebWYAmh08U89Oi5VnO1gAzHJ5N3M0bQs8xgWfEC1cRqVBD/sHJAk+rDvSgTXj/+3DEULnWbf7Dr5FpLMyKeLMrkk0Z/6dQ2qiJJ3BEeeR1+AcJ3Vg==
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