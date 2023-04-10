

  

# RocketFuel JS SDK Page

Follow the guide to embed RocketFuel Hosted Page on the Merchant's website.

# Prerequistes:-

Merchant should send below JSON input details to display the same on RocketFuel hosted page

  

    Sample Format of JSON Data-
    {
	    amount: "11.00",
	    merchant_id: process.env.MERCHANT_ID,
	    cart: [
	    {
		    id: "23",
		    name: "Album",
		    price: "11",
		    quantity: "1",
	    },
	    ],
		 currency: "USD",
	    order: "390",
	    redirectUrl: "",
    }

  

where

  

1. Amount - total price of whole order for payment, in USD only
2. Cart -
	a. ID - Article unique Id
	b. Name - Article Name
	c. Price - Article price
	d. Quantity - Article Quantity
3. Currency - Currency in which Merchant recieved the payment, in USD only
4. Order - Unique Order Id
5. RedirectUrl - URL of the Merchant site where the Merchant wants to redirect from the Hosted page to their site after payment.

  

### Follow the steps below :-

  
1. Merchant need to be authenticated on RocketFuel by passing the input parameters - MERCHANT_EMAIL and MERCHANT_PASSWORD.

  

**Request**


    var options = {
    	method: "POST",
    	url: process.env.API_ENDPOINT + "auth/login",
	    headers: {
		    "Content-Type": "application/json",
	    },
	    body: JSON.stringify({
		    email: process.env.MERCHANT_EMAIL,
		    password: process.env.MERCHANT_PASS,
	    }),
    };

  

  

**Response**

  

  

    { "ok":true, "result"{ "access":"eyJhbGciOiJIUzI1NiIsInR5cCI6Ikp
    XVCJ9.eyJpZCI6ImViMDE1NWU4 LTkwNzItNGMyYi05NWFjLTAxZDhiMWFlZDI3ZC
    IsImlhdCI6MTYyMzQwMz g0OSwiZXhwIjoxNjIzNDkwMjQ5fQ.t1wL6LYkr8y5sau
    CuOWMmGbGNDZH qXzUjo6WeT370c","refresh":"eyJhbGciOiJIUzI1NiIsInR5
    cCI6IkpXVCJ9.eyJpZC I6ImViMDE1NWU4LTkwNzItNGMyYi05NWFjLTAxZDhiMWF
    lZDI3ZCIsImlhd CI6MTYyMzQwMzg0OSwiZXhwIjoxNjI1OTk1ODQ5fQ.A_JF1ODt
    cRCRc7Yn TcT5JBFDDMkgQtlQXYkhBFw3dgM", "status":2 }

  
  

2. Once Merchant is verified, details of the items purchased with the access token will be sent in a different request.

Note - Token needs to send in the Header and details of the items purchased in the Body.

  

**Request**

  

    var options = {
	    method: "POST",
	    url: process.env.API_ENDPOINT + "hosted-page",
	    headers: {
		    authorization: "Bearer " + accessToken,
		    "Content-Type": "application/json",
	    },
	    body: JSON.stringify({
	    amount: "11.00",
	    merchant_id: process.env.MERCHANT_ID,
	    cart: [
	    {
		    id: "23",
		    name: "Album",
		    price: "11",
		    quantity: "1",
	    },
	    ],
	    currency: "USD",
	    order: "390",
	    redirectUrl: "",
	    }),
    };

  

**Response** -

  

    {
	    "ok":true,
	    "result":{"url":"[https://dev.rocketdemo.net/hostedPage
	    /f7cb4141-3030-4245-8aa1-](https://dev.rocketdemo.net/hostedP
	    age/f7cb4141-3030-4245-8aa1-)[f5cf95b5d504](https://dev.rocke
	    tdemo.net/hostedPage/f7cb4141-3030-4245-8aa1-f5cf95b5d504)"}
    
    }
3. Once Merchant received the response as a True, then send the UUID as the response  to  the Merchant site.

***On click of [Pay for your order with RocketFuel] paste below Code Snippet.***

**Code Snippet--**

    const request=require('request');
    var options = {
    
	    method: "POST",
	    url: process.env.API_ENDPOINT + "auth/login",
	    headers: {
	    "Content-Type": "application/json",
	    },
	    body: JSON.stringify({
		    email: process.env.MERCHANT_EMAIL,
		    password: process.env.MERCHANT_PASS,
	    }),
    };
    
    request(options, function (error, response) {
    
	    if (error) throw new Error(error);    
	    let accessToken = JSON.parse(response.body).result.access;
    
	    //place the order API Call
	    var options = {
		    method: "POST",
		    url: process.env.API_ENDPOINT + "hosted-page",
		    headers: {
			    authorization: "Bearer " + accessToken,
			    "Content-Type": "application/const" 
			    } 
		    },
		    body: JSON.stringify({
		    amount: "11.00",
		    merchant_id: process.env.MERCHANT_ID,
		    cart: [
		    {
			    id: "23",
			    name: "Album",
			    price: "11",
			    quantity: "1",
			   },
		   ],
		    currency: "USD",
		    order: "390",
		    redirectUrl: "",
    }),
    };
    
    request(options, function (error, response) {
    
	    if (error) throw new Error(error);
	    let resp = JSON.parse(response.body);
	    if(resp.result !== undefined && resp.result.url !==undefined){
			let  urlArr = resp.result.url.split("/");
			let  uuid = urlArr[urlArr.length - 1];
			res.status(200).send({ uuid:  uuid });
		}else{
			res.status(400).send({ error:  "Failed to place order" });
			}
		 });
    });
# Javascript Library
4.  **RKFL JS [CDN] and Implementation**

	4.1. Add the script from **[CDN]** to the Merchant site.  

	4.2	Once we get the response with the uuid. We will initialise an object of the above included script, while initialising the object we will pass following :- 
	-   uuid
	-   callback function
	-   environment
	-   Token

		

			const  uuidInfo = JSON.parse(result);
			if(uuidInfo.error !== undefined){
				alert("Order placement failed");
				return  false;
			}
			uuid = uuidInfo.uuid;
			rkfl = new  RocketFuel({
				uuid,
				callback:  callBackFunc,
				environment:  "<%= developmentEnv %>"  // prod, preprod
			});
	
4.3. After initialising the object start the payment by calling the initPayment method of the above script.
	

		function  startPayment(){
			rkfl.initPayment();
		}

4.4. Callback payload
	 
	 // In case of Bank/Exchange payment
    	      {
                paymentMode: 'Bank/Exchange',
                txn_id: 
                status: 
                meta:
              },
    
    		  Sample response:
    		  {
                paymentMode: 'Bank/Exchange',
                txn_id: "7df55d22-fa5e-4ca2-9af4-a39c95f18b3a"
                status: 0
                meta: {offerId: "1630402767550"}
              },
		 


	// In case of Wallet payment
	      {
            paymentMode: 'Wallet',
            status: 
            recievedAmount:
            currency:
          },

           Sample response:
		   {
			   paymentMode: 'Wallet',
			   status:"completed",
			   recievedAmount:10.00,
			   currency:"ETH"
		   }

## You can refer to the [REFERENCE_LINK] for demonstration.

   [CDN]: <https://d3rpjm0wf8u2co.cloudfront.net/static/rkfl.js>
   [PUBLIC_KEY]: <https://bitbucket.org/rocketfuelblockchain/rocketfuel-readme/src/master/notification.md>
   [REFERENCE_LINK]: <https://bitbucket.org/rocketfuelblockchain/rocketfuel-demo-hosted-v2/src/master/>