<div class="editormd pui-z-depth-3 pui-bg-white editormd-vertical" id="index-editormd" style="width: 100%; height: 580px;"><textarea class="editormd-markdown-textarea" placeholder="Enjoy Markdown! coding now..." name="index-editormd-markdown-doc" style="display: none;">

  

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
3. Once Merchant received the response as a True, then send the UUID as the response  to  the Merchant site.

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
        if(resp.result !== undefined &amp;&amp; resp.result.url !==undefined){
            let  urlArr = resp.result.url.split("/");
            let  uuid = urlArr[urlArr.length - 1];
            res.status(200).send({ uuid:  uuid });
        }else{
            res.status(400).send({ error:  "Failed to place order" });
            }
         });
    });
# Javascript Library
4.  **RKFL JS [CDN] and Implementation**

    4.1. Add the script from **[CDN]** to the Merchant site.  

    4.2    Once we get the response with the uuid. We will initialise an object of the above included script, while initialising the object we will pass following :- 
    -   uuid
    -   callback function
    -   environment
    -   Token

        

            const  uuidInfo = JSON.parse(result);
            if(uuidInfo.error !== undefined){
                alert("Order placement failed");
                return  false;
            }
            uuid = uuidInfo.uuid;
            rkfl = new  RocketFuel({
                uuid,
                callback:  callBackFunc,
                environment:  "&lt;%= developmentEnv %&gt;"  // prod, preprod
            });
    
4.3. After initialising the object start the payment by calling the initPayment method of the above script.
    

        function  startPayment(){
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

# SSO Login

1. Create merchant Auth using the [PUBLIC_KEY]
    - JS Code snippet

            var merchantAuth = function(merchantId) {
                var buffer = Buffer.from(merchantId);
                var encrypted = crypto.publicEncrypt(process.env.PUBLIC_KEY, buffer);
                return encrypted.toString("base64");
            }
2.  RKFL Token usage
    - Autosignup 
```
            const payload = {
                firstName: firstName,
                lastName, lastName,
                email: email,
                merchantAuth: "&lt;%= merchantAuth %&gt;",
            }
            rkfl = new RocketFuel({ environment: "&lt;%= developmentEnv %&gt;", });
            rkfl.rkflAutoSignUp(payload, environment = "&lt;%= developmentEnv %&gt;").then((res) =&gt; {
                // save this rkflToken for reference in the DB 
                // It is unique to each customer
                res.result.rkflToken;
            })})
```            
    - Existing RKFL Token
```    
            rkfl = new  RocketFuel({
                token, // rkfltoken
                uuid,
                callback:  callBackFunc,
                environment:  "&lt;%= developmentEnv %&gt;"  // prod, preprod
            })
```
## You can refer to the [REFERENCE_LINK] for demonstration.

   [CDN]: &lt;https://d3rpjm0wf8u2co.cloudfront.net/static/rkfl.js&gt;
   [PUBLIC_KEY]: &lt;https://bitbucket.org/rocketfuelblockchain/rocketfuel-readme/src/master/notification.md&gt;
   [REFERENCE_LINK]: &lt;https://bitbucket.org/rocketfuelblockchain/rocketfuel-demo-hosted-v2/src/master/&gt;</textarea><div class="CodeMirror cm-s-default CodeMirror-wrap" style="font-size: 13px; width: 298px; margin-top: 119px; height: 462px; display: none;"><div style="overflow: hidden; position: relative; width: 3px; height: 0px; top: 528px; left: 224.325px;"><textarea autocorrect="off" autocapitalize="off" spellcheck="false" style="position: absolute; padding: 0px; width: 1000px; height: 1em; outline: none;" class="" tabindex="0"></textarea></div><div class="CodeMirror-vscrollbar" cm-not-content="true" style="display: block; bottom: 0px;"><div style="min-width: 1px; height: 7505px;"></div></div><div class="CodeMirror-hscrollbar" cm-not-content="true"><div style="height: 100%; min-height: 1px; width: 0px;"></div></div><div class="CodeMirror-scrollbar-filler" cm-not-content="true"></div><div class="CodeMirror-gutter-filler" cm-not-content="true"></div><div class="CodeMirror-scroll" tabindex="-1"><div class="CodeMirror-sizer" style="margin-left: 52px; margin-bottom: -7px; border-right-width: 23px; min-height: 7502px; padding-right: 7px; padding-bottom: 0px;"><div style="position: relative; top: 0px;"><div class="CodeMirror-lines"><div style="position: relative; outline: none;"><div class="CodeMirror-measure"><pre>x</pre></div><div class="CodeMirror-measure"></div><div style="position: relative; z-index: 1;"></div><div class="CodeMirror-cursors" style=""><div class="CodeMirror-cursor" style="left: 172.325px; top: 7470px; height: 23.6px;">&nbsp;</div></div><div class="CodeMirror-code" style=""><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">1</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">2</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">3</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-cm-overlay cm-trailingspace">  </span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">4</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">5</div><div class="CodeMirror-gutter-elt" style="left: 41px; width: 10px;"><div class="CodeMirror-foldgutter-open CodeMirror-guttermarker-subtle"></div></div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-header cm-header-1"># RocketFuel JS SDK Page</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">6</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">7</div></div><pre class=""><span style="padding-right: 0.1px;">Follow the guide to embed RocketFuel Hosted Page on the Merchant's website.</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">8</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">9</div><div class="CodeMirror-gutter-elt" style="left: 41px; width: 10px;"><div class="CodeMirror-foldgutter-open CodeMirror-guttermarker-subtle"></div></div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-header cm-header-1"># Prerequistes:-</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">10</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">11</div></div><pre class=""><span style="padding-right: 0.1px;">Merchant should send below JSON input details to display the same on RocketFuel hosted page</span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">12</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">13</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-cm-overlay cm-trailingspace">  </span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">14</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">15</div></div><pre class=""><span style="padding-right: 0.1px;"> &nbsp;  <span class="cm-comment">Sample Format of JSON Data-</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">16</div></div><pre class=""><span style="padding-right: 0.1px;"> &nbsp;  <span class="cm-comment">{</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">17</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">amount: "11.00",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">18</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">merchant_id: process.env.MERCHANT_ID,</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">19</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">cart: [</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">20</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">{</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">21</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">id: "23",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">22</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">name: "Album",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">23</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">price: "11",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">24</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">quantity: "1",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">25</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">},</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">26</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">],</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">27</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span><span class="cm-tab" role="presentation" cm-text="    ">    </span> <span class="cm-comment">currency: "USD",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">28</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">order: "390",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">29</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-tab" role="presentation" cm-text="    ">    </span> &nbsp;  <span class="cm-comment">redirectUrl: "",</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">30</div></div><pre class=""><span style="padding-right: 0.1px;"> &nbsp;  <span class="cm-comment">}</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">31</div></div><pre class=""><span style="padding-right: 0.1px;"><span cm-text="">&ZeroWidthSpace;</span></span></pre></div><div style="position: relative;"><div class="CodeMirror-gutter-wrapper" style="left: -52px; width: 51px;"><div class="CodeMirror-linenumber CodeMirror-gutter-elt" style="left: 0px; width: 23px;">32</div></div><pre class=""><span style="padding-right: 0.1px;"><span class="cm-cm-overlay cm-trailingspace">  </span></span></pre></div></div></div></div></div></div><div style="position: absolute; height: 23px; width: 1px; top: 7502px;"></div><div class="CodeMirror-gutters" style="height: 7525px;"><div class="CodeMirror-gutter CodeMirror-linenumbers" style="width: 31px;"></div><div class="CodeMirror-gutter CodeMirror-foldgutter"></div></div></div></div><a href="javascript:;" class="fa fa-close editormd-preview-close-btn" style="display: inline;"></a>

<div class="editormd-preview" style="display: block; width: 594px; top: 119px; height: 462px;"><div class="markdown-body editormd-preview-container editormd-preview-active" previewcontainer="true" style="padding: 20px;"><h1 id="h1-rocketfuel-js-sdk-page"><a name="RocketFuel JS SDK Page" class="reference-link"></a><span class="header-link octicon octicon-link"></span>RocketFuel JS SDK Page</h1><p>Follow the guide to embed RocketFuel Hosted Page on the Merchant’s website.</p>
<h1 id="h1-prerequistes-"><a name="Prerequistes:-" class="reference-link"></a><span class="header-link octicon octicon-link"></span>Prerequistes:-</h1><p>Merchant should send below JSON input details to display the same on RocketFuel hosted page</p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="typ">Sample</span><span class="pln"> </span><span class="typ">Format</span><span class="pln"> of JSON </span><span class="typ">Data</span><span class="pun">-</span></code></li><li class="L1"><code><span class="pun">{</span></code></li><li class="L2"><code><span class="pln">    amount</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11.00"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">    merchant_id</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_ID</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">    cart</span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span></code></li><li class="L5"><code><span class="pln">    </span><span class="pun">{</span></code></li><li class="L6"><code><span class="pln">        id</span><span class="pun">:</span><span class="pln"> </span><span class="str">"23"</span><span class="pun">,</span></code></li><li class="L7"><code><span class="pln">        name</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Album"</span><span class="pun">,</span></code></li><li class="L8"><code><span class="pln">        price</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11"</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">        quantity</span><span class="pun">:</span><span class="pln"> </span><span class="str">"1"</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">    </span><span class="pun">},</span></code></li><li class="L1"><code><span class="pln">    </span><span class="pun">],</span></code></li><li class="L2"><code><span class="pln">     currency</span><span class="pun">:</span><span class="pln"> </span><span class="str">"USD"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">    order</span><span class="pun">:</span><span class="pln"> </span><span class="str">"390"</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">    redirectUrl</span><span class="pun">:</span><span class="pln"> </span><span class="str">""</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pun">}</span></code></li></ol></pre><p>where</p>
<ol>
<li>Amount - total price of whole order for payment, in USD only</li><li>Cart -<br> a. ID - Article unique Id<br> b. Name - Article Name<br> c. Price - Article price<br> d. Quantity - Article Quantity</li><li>Currency - Currency in which Merchant recieved the payment, in USD only</li><li>Order - Unique Order Id</li><li>RedirectUrl - URL of the Merchant site where the Merchant wants to redirect from the Hosted page to their site after payment.</li></ol>
<h3 id="h3-follow-the-steps-below-"><a name="Follow the steps below :-" class="reference-link"></a><span class="header-link octicon octicon-link"></span>Follow the steps below :-</h3><ol>
<li>Merchant need to be authenticated on RocketFuel by passing the input parameters - MERCHANT_EMAIL and MERCHANT_PASSWORD.</li></ol>
<p><strong>Request</strong></p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="kwd">var</span><span class="pln"> options </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">    method</span><span class="pun">:</span><span class="pln"> </span><span class="str">"POST"</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pln">    url</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">API_ENDPOINT </span><span class="pun">+</span><span class="pln"> </span><span class="str">"auth/login"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">    headers</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L4"><code><span class="pln">        </span><span class="str">"Content-Type"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"application/json"</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">    </span><span class="pun">},</span></code></li><li class="L6"><code><span class="pln">    body</span><span class="pun">:</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">stringify</span><span class="pun">({</span></code></li><li class="L7"><code><span class="pln">        email</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_EMAIL</span><span class="pun">,</span></code></li><li class="L8"><code><span class="pln">        password</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_PASS</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">    </span><span class="pun">}),</span></code></li><li class="L0"><code><span class="pun">};</span></code></li></ol></pre><p><strong>Response</strong></p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pun">{</span><span class="pln"> </span><span class="str">"ok"</span><span class="pun">:</span><span class="kwd">true</span><span class="pun">,</span><span class="pln"> </span><span class="str">"result"</span><span class="pun">{</span><span class="pln"> </span><span class="str">"access"</span><span class="pun">:</span><span class="str">"eyJhbGciOiJIUzI1NiIsInR5cCI6Ikp</span></code></li><li class="L1"><code><span class="str">XVCJ9.eyJpZCI6ImViMDE1NWU4 LTkwNzItNGMyYi05NWFjLTAxZDhiMWFlZDI3ZC</span></code></li><li class="L2"><code><span class="str">IsImlhdCI6MTYyMzQwMz g0OSwiZXhwIjoxNjIzNDkwMjQ5fQ.t1wL6LYkr8y5sau</span></code></li><li class="L3"><code><span class="str">CuOWMmGbGNDZH qXzUjo6WeT370c"</span><span class="pun">,</span><span class="str">"refresh"</span><span class="pun">:</span><span class="str">"eyJhbGciOiJIUzI1NiIsInR5</span></code></li><li class="L4"><code><span class="str">cCI6IkpXVCJ9.eyJpZC I6ImViMDE1NWU4LTkwNzItNGMyYi05NWFjLTAxZDhiMWF</span></code></li><li class="L5"><code><span class="str">lZDI3ZCIsImlhd CI6MTYyMzQwMzg0OSwiZXhwIjoxNjI1OTk1ODQ5fQ.A_JF1ODt</span></code></li><li class="L6"><code><span class="str">cRCRc7Yn TcT5JBFDDMkgQtlQXYkhBFw3dgM"</span><span class="pun">,</span><span class="pln"> </span><span class="str">"status"</span><span class="pun">:</span><span class="lit">2</span><span class="pln"> </span><span class="pun">}</span></code></li></ol></pre><ol>
<li>Once Merchant is verified, details of the items purchased with the access token will be sent in a different request.</li></ol>
<p>Note - Token needs to send in the Header and details of the items purchased in the Body.</p>
<p><strong>Request</strong></p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="kwd">var</span><span class="pln"> options </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">    method</span><span class="pun">:</span><span class="pln"> </span><span class="str">"POST"</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pln">    url</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">API_ENDPOINT </span><span class="pun">+</span><span class="pln"> </span><span class="str">"hosted-page"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">    headers</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L4"><code><span class="pln">        authorization</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Bearer "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> accessToken</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">        </span><span class="str">"Content-Type"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"application/json"</span><span class="pun">,</span></code></li><li class="L6"><code><span class="pln">    </span><span class="pun">},</span></code></li><li class="L7"><code><span class="pln">    body</span><span class="pun">:</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">stringify</span><span class="pun">({</span></code></li><li class="L8"><code><span class="pln">    amount</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11.00"</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">    merchant_id</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_ID</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">    cart</span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span></code></li><li class="L1"><code><span class="pln">    </span><span class="pun">{</span></code></li><li class="L2"><code><span class="pln">        id</span><span class="pun">:</span><span class="pln"> </span><span class="str">"23"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">        name</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Album"</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">        price</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11"</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">        quantity</span><span class="pun">:</span><span class="pln"> </span><span class="str">"1"</span><span class="pun">,</span></code></li><li class="L6"><code><span class="pln">    </span><span class="pun">},</span></code></li><li class="L7"><code><span class="pln">    </span><span class="pun">],</span></code></li><li class="L8"><code><span class="pln">    currency</span><span class="pun">:</span><span class="pln"> </span><span class="str">"USD"</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">    order</span><span class="pun">:</span><span class="pln"> </span><span class="str">"390"</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">    redirectUrl</span><span class="pun">:</span><span class="pln"> </span><span class="str">""</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">    </span><span class="pun">}),</span></code></li><li class="L2"><code><span class="pun">};</span></code></li></ol></pre><p><strong>Response</strong> -</p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">    </span><span class="str">"ok"</span><span class="pun">:</span><span class="kwd">true</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pln">    </span><span class="str">"result"</span><span class="pun">:{</span><span class="str">"url"</span><span class="pun">:</span><span class="str">"[https://dev.rocketdemo.net/hostedPage</span></code></li><li class="L3"><code><span class="str">    /f7cb4141-3030-4245-8aa1-](https://dev.rocketdemo.net/hostedP</span></code></li><li class="L4"><code><span class="str">    age/f7cb4141-3030-4245-8aa1-)[f5cf95b5d504](https://dev.rocke</span></code></li><li class="L5"><code><span class="str">    tdemo.net/hostedPage/f7cb4141-3030-4245-8aa1-f5cf95b5d504)"</span><span class="pun">}</span></code></li><li class="L6"><code></code></li><li class="L7"><code><span class="pun">}</span></code></li></ol></pre><ol>
<li>Once Merchant received the response as a True, then send the UUID as the response  to  the Merchant site.</li></ol>
<p><strong><em>On click of [Pay for your order with RocketFuel] paste below Code Snippet.</em></strong></p>
<p><strong>Code Snippet—</strong></p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="kwd">const</span><span class="pln"> request</span><span class="pun">=</span><span class="kwd">require</span><span class="pun">(</span><span class="str">'request'</span><span class="pun">);</span></code></li><li class="L1"><code><span class="kwd">var</span><span class="pln"> options </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L2"><code></code></li><li class="L3"><code><span class="pln">    method</span><span class="pun">:</span><span class="pln"> </span><span class="str">"POST"</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">    url</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">API_ENDPOINT </span><span class="pun">+</span><span class="pln"> </span><span class="str">"auth/login"</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">    headers</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L6"><code><span class="pln">    </span><span class="str">"Content-Type"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"application/json"</span><span class="pun">,</span></code></li><li class="L7"><code><span class="pln">    </span><span class="pun">},</span></code></li><li class="L8"><code><span class="pln">    body</span><span class="pun">:</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">stringify</span><span class="pun">({</span></code></li><li class="L9"><code><span class="pln">        email</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_EMAIL</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">        password</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_PASS</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">    </span><span class="pun">}),</span></code></li><li class="L2"><code><span class="pun">};</span></code></li><li class="L3"><code></code></li><li class="L4"><code><span class="pln">request</span><span class="pun">(</span><span class="pln">options</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">function</span><span class="pln"> </span><span class="pun">(</span><span class="pln">error</span><span class="pun">,</span><span class="pln"> response</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L5"><code></code></li><li class="L6"><code><span class="pln">    </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">error</span><span class="pun">)</span><span class="pln"> </span><span class="kwd">throw</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">Error</span><span class="pun">(</span><span class="pln">error</span><span class="pun">);</span><span class="pln">    </span></code></li><li class="L7"><code><span class="pln">    </span><span class="kwd">let</span><span class="pln"> accessToken </span><span class="pun">=</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">parse</span><span class="pun">(</span><span class="pln">response</span><span class="pun">.</span><span class="pln">body</span><span class="pun">).</span><span class="pln">result</span><span class="pun">.</span><span class="pln">access</span><span class="pun">;</span></code></li><li class="L8"><code></code></li><li class="L9"><code><span class="pln">    </span><span class="com">//place the order API Call</span></code></li><li class="L0"><code><span class="pln">    </span><span class="kwd">var</span><span class="pln"> options </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">        method</span><span class="pun">:</span><span class="pln"> </span><span class="str">"POST"</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pln">        url</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">API_ENDPOINT </span><span class="pun">+</span><span class="pln"> </span><span class="str">"hosted-page"</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">        headers</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L4"><code><span class="pln">            authorization</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Bearer "</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> accessToken</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">            </span><span class="str">"Content-Type"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"application/const"</span><span class="pln"> </span></code></li><li class="L6"><code><span class="pln">            </span><span class="pun">}</span><span class="pln"> </span></code></li><li class="L7"><code><span class="pln">        </span><span class="pun">},</span></code></li><li class="L8"><code><span class="pln">        body</span><span class="pun">:</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">stringify</span><span class="pun">({</span></code></li><li class="L9"><code><span class="pln">        amount</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11.00"</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">        merchant_id</span><span class="pun">:</span><span class="pln"> process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">MERCHANT_ID</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">        cart</span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span></code></li><li class="L2"><code><span class="pln">        </span><span class="pun">{</span></code></li><li class="L3"><code><span class="pln">            id</span><span class="pun">:</span><span class="pln"> </span><span class="str">"23"</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">            name</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Album"</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">            price</span><span class="pun">:</span><span class="pln"> </span><span class="str">"11"</span><span class="pun">,</span></code></li><li class="L6"><code><span class="pln">            quantity</span><span class="pun">:</span><span class="pln"> </span><span class="str">"1"</span><span class="pun">,</span></code></li><li class="L7"><code><span class="pln">           </span><span class="pun">},</span></code></li><li class="L8"><code><span class="pln">       </span><span class="pun">],</span></code></li><li class="L9"><code><span class="pln">        currency</span><span class="pun">:</span><span class="pln"> </span><span class="str">"USD"</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">        order</span><span class="pun">:</span><span class="pln"> </span><span class="str">"390"</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">        redirectUrl</span><span class="pun">:</span><span class="pln"> </span><span class="str">""</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pun">}),</span></code></li><li class="L3"><code><span class="pun">};</span></code></li><li class="L4"><code></code></li><li class="L5"><code><span class="pln">request</span><span class="pun">(</span><span class="pln">options</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">function</span><span class="pln"> </span><span class="pun">(</span><span class="pln">error</span><span class="pun">,</span><span class="pln"> response</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L6"><code></code></li><li class="L7"><code><span class="pln">    </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">error</span><span class="pun">)</span><span class="pln"> </span><span class="kwd">throw</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">Error</span><span class="pun">(</span><span class="pln">error</span><span class="pun">);</span></code></li><li class="L8"><code><span class="pln">    </span><span class="kwd">let</span><span class="pln"> resp </span><span class="pun">=</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">parse</span><span class="pun">(</span><span class="pln">response</span><span class="pun">.</span><span class="pln">body</span><span class="pun">);</span></code></li><li class="L9"><code><span class="pln">    </span><span class="kwd">if</span><span class="pun">(</span><span class="pln">resp</span><span class="pun">.</span><span class="pln">result </span><span class="pun">!==</span><span class="pln"> </span><span class="kwd">undefined</span><span class="pln"> </span><span class="pun">&amp;&amp;</span><span class="pln"> resp</span><span class="pun">.</span><span class="pln">result</span><span class="pun">.</span><span class="pln">url </span><span class="pun">!==</span><span class="kwd">undefined</span><span class="pun">){</span></code></li><li class="L0"><code><span class="pln">        </span><span class="kwd">let</span><span class="pln">  urlArr </span><span class="pun">=</span><span class="pln"> resp</span><span class="pun">.</span><span class="pln">result</span><span class="pun">.</span><span class="pln">url</span><span class="pun">.</span><span class="pln">split</span><span class="pun">(</span><span class="str">"/"</span><span class="pun">);</span></code></li><li class="L1"><code><span class="pln">        </span><span class="kwd">let</span><span class="pln">  uuid </span><span class="pun">=</span><span class="pln"> urlArr</span><span class="pun">[</span><span class="pln">urlArr</span><span class="pun">.</span><span class="pln">length </span><span class="pun">-</span><span class="pln"> </span><span class="lit">1</span><span class="pun">];</span></code></li><li class="L2"><code><span class="pln">        res</span><span class="pun">.</span><span class="pln">status</span><span class="pun">(</span><span class="lit">200</span><span class="pun">).</span><span class="pln">send</span><span class="pun">({</span><span class="pln"> uuid</span><span class="pun">:</span><span class="pln">  uuid </span><span class="pun">});</span></code></li><li class="L3"><code><span class="pln">    </span><span class="pun">}</span><span class="kwd">else</span><span class="pun">{</span></code></li><li class="L4"><code><span class="pln">        res</span><span class="pun">.</span><span class="pln">status</span><span class="pun">(</span><span class="lit">400</span><span class="pun">).</span><span class="pln">send</span><span class="pun">({</span><span class="pln"> error</span><span class="pun">:</span><span class="pln">  </span><span class="str">"Failed to place order"</span><span class="pln"> </span><span class="pun">});</span></code></li><li class="L5"><code><span class="pln">        </span><span class="pun">}</span></code></li><li class="L6"><code><span class="pln">     </span><span class="pun">});</span></code></li><li class="L7"><code><span class="pun">});</span></code></li></ol></pre><h1 id="h1-javascript-library"><a name="Javascript Library" class="reference-link"></a><span class="header-link octicon octicon-link"></span>Javascript Library</h1><ol>
<li><p><strong>RKFL JS <a href="https://d3rpjm0wf8u2co.cloudfront.net/static/rkfl.js">CDN</a> and Implementation</strong></p>
<p>4.1. Add the script from <strong><a href="https://d3rpjm0wf8u2co.cloudfront.net/static/rkfl.js">CDN</a></strong> to the Merchant site.  </p>
<p>4.2    Once we get the response with the uuid. We will initialise an object of the above included script, while initialising the object we will pass following :- </p>
<ul>
<li>uuid</li><li>callback function</li><li>environment</li><li>Token</li></ul>
</li></ol>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln">        </span><span class="kwd">const</span><span class="pln">  uuidInfo </span><span class="pun">=</span><span class="pln"> JSON</span><span class="pun">.</span><span class="pln">parse</span><span class="pun">(</span><span class="pln">result</span><span class="pun">);</span></code></li><li class="L1"><code><span class="pln">        </span><span class="kwd">if</span><span class="pun">(</span><span class="pln">uuidInfo</span><span class="pun">.</span><span class="pln">error </span><span class="pun">!==</span><span class="pln"> </span><span class="kwd">undefined</span><span class="pun">){</span></code></li><li class="L2"><code><span class="pln">            alert</span><span class="pun">(</span><span class="str">"Order placement failed"</span><span class="pun">);</span></code></li><li class="L3"><code><span class="pln">            </span><span class="kwd">return</span><span class="pln">  </span><span class="kwd">false</span><span class="pun">;</span></code></li><li class="L4"><code><span class="pln">        </span><span class="pun">}</span></code></li><li class="L5"><code><span class="pln">        uuid </span><span class="pun">=</span><span class="pln"> uuidInfo</span><span class="pun">.</span><span class="pln">uuid</span><span class="pun">;</span></code></li><li class="L6"><code><span class="pln">        rkfl </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln">  </span><span class="typ">RocketFuel</span><span class="pun">({</span></code></li><li class="L7"><code><span class="pln">            uuid</span><span class="pun">,</span></code></li><li class="L8"><code><span class="pln">            callback</span><span class="pun">:</span><span class="pln">  callBackFunc</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">            environment</span><span class="pun">:</span><span class="pln">  </span><span class="str">"&lt;%= developmentEnv %&gt;"</span><span class="pln">  </span><span class="com">// prod, preprod</span></code></li><li class="L0"><code><span class="pln">        </span><span class="pun">});</span></code></li></ol></pre><p>4.3. After initialising the object start the payment by calling the initPayment method of the above script.</p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln">    </span><span class="kwd">function</span><span class="pln">  startPayment</span><span class="pun">(){</span></code></li><li class="L1"><code><span class="pln">        rkfl</span><span class="pun">.</span><span class="pln">initPayment</span><span class="pun">();</span></code></li><li class="L2"><code><span class="pln">    </span><span class="pun">}</span></code></li></ol></pre><p>4.4. Callback payload</p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln"> </span><span class="com">// In case of Bank/Exchange payment</span></code></li><li class="L1"><code><span class="pln">          </span><span class="pun">{</span></code></li><li class="L2"><code><span class="pln">            paymentMode</span><span class="pun">:</span><span class="pln"> </span><span class="str">'Bank/Exchange'</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">            txn_id</span><span class="pun">:</span><span class="pln"> </span></code></li><li class="L4"><code><span class="pln">            status</span><span class="pun">:</span><span class="pln"> </span></code></li><li class="L5"><code><span class="pln">            meta</span><span class="pun">:</span></code></li><li class="L6"><code><span class="pln">          </span><span class="pun">},</span></code></li><li class="L7"><code></code></li><li class="L8"><code><span class="pln">          </span><span class="typ">Sample</span><span class="pln"> response</span><span class="pun">:</span></code></li><li class="L9"><code><span class="pln">          </span><span class="pun">{</span></code></li><li class="L0"><code><span class="pln">            paymentMode</span><span class="pun">:</span><span class="pln"> </span><span class="str">'Bank/Exchange'</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">            txn_id</span><span class="pun">:</span><span class="pln"> </span><span class="str">"7df55d22-fa5e-4ca2-9af4-a39c95f18b3a"</span></code></li><li class="L2"><code><span class="pln">            status</span><span class="pun">:</span><span class="pln"> </span><span class="lit">0</span></code></li><li class="L3"><code><span class="pln">            meta</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span><span class="pln">offerId</span><span class="pun">:</span><span class="pln"> </span><span class="str">"1630402767550"</span><span class="pun">}</span></code></li><li class="L4"><code><span class="pln">          </span><span class="pun">},</span></code></li><li class="L5"><code></code></li><li class="L6"><code></code></li><li class="L7"><code></code></li><li class="L8"><code><span class="com">// In case of Wallet payment</span></code></li><li class="L9"><code><span class="pln">      </span><span class="pun">{</span></code></li><li class="L0"><code><span class="pln">        paymentMode</span><span class="pun">:</span><span class="pln"> </span><span class="str">'Wallet'</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">        status</span><span class="pun">:</span><span class="pln"> </span></code></li><li class="L2"><code><span class="pln">        recievedAmount</span><span class="pun">:</span></code></li><li class="L3"><code><span class="pln">        currency</span><span class="pun">:</span></code></li><li class="L4"><code><span class="pln">      </span><span class="pun">},</span></code></li><li class="L5"><code></code></li><li class="L6"><code><span class="pln">       </span><span class="typ">Sample</span><span class="pln"> response</span><span class="pun">:</span></code></li><li class="L7"><code><span class="pln">       </span><span class="pun">{</span></code></li><li class="L8"><code><span class="pln">           paymentMode</span><span class="pun">:</span><span class="pln"> </span><span class="str">'Wallet'</span><span class="pun">,</span></code></li><li class="L9"><code><span class="pln">           status</span><span class="pun">:</span><span class="str">"completed"</span><span class="pun">,</span></code></li><li class="L0"><code><span class="pln">           recievedAmount</span><span class="pun">:</span><span class="lit">10.00</span><span class="pun">,</span></code></li><li class="L1"><code><span class="pln">           currency</span><span class="pun">:</span><span class="str">"ETH"</span></code></li><li class="L2"><code><span class="pln">       </span><span class="pun">}</span></code></li></ol></pre><h1 id="h1-sso-login"><a name="SSO Login" class="reference-link"></a><span class="header-link octicon octicon-link"></span>SSO Login</h1><ol>
<li><p>Create merchant Auth using the <a href="https://bitbucket.org/rocketfuelblockchain/rocketfuel-readme/src/master/notification.md">PUBLIC_KEY</a></p>
<ul>
<li><p>JS Code snippet</p>
<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln">  </span><span class="kwd">var</span><span class="pln"> merchantAuth </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">function</span><span class="pun">(</span><span class="pln">merchantId</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">      </span><span class="kwd">var</span><span class="pln"> buffer </span><span class="pun">=</span><span class="pln"> </span><span class="typ">Buffer</span><span class="pun">.</span><span class="kwd">from</span><span class="pun">(</span><span class="pln">merchantId</span><span class="pun">);</span></code></li><li class="L2"><code><span class="pln">      </span><span class="kwd">var</span><span class="pln"> encrypted </span><span class="pun">=</span><span class="pln"> crypto</span><span class="pun">.</span><span class="pln">publicEncrypt</span><span class="pun">(</span><span class="pln">process</span><span class="pun">.</span><span class="pln">env</span><span class="pun">.</span><span class="pln">PUBLIC_KEY</span><span class="pun">,</span><span class="pln"> buffer</span><span class="pun">);</span></code></li><li class="L3"><code><span class="pln">      </span><span class="kwd">return</span><span class="pln"> encrypted</span><span class="pun">.</span><span class="pln">toString</span><span class="pun">(</span><span class="str">"base64"</span><span class="pun">);</span></code></li><li class="L4"><code><span class="pln">  </span><span class="pun">}</span></code></li></ol></pre></li></ul>
</li><li>RKFL Token usage<ul>
<li>Autosignup <pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln">      </span><span class="kwd">const</span><span class="pln"> payload </span><span class="pun">=</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L1"><code><span class="pln">          firstName</span><span class="pun">:</span><span class="pln"> firstName</span><span class="pun">,</span></code></li><li class="L2"><code><span class="pln">          lastName</span><span class="pun">,</span><span class="pln"> lastName</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">          email</span><span class="pun">:</span><span class="pln"> email</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">          merchantAuth</span><span class="pun">:</span><span class="pln"> </span><span class="str">"&lt;%= merchantAuth %&gt;"</span><span class="pun">,</span></code></li><li class="L5"><code><span class="pln">      </span><span class="pun">}</span></code></li><li class="L6"><code><span class="pln">      rkfl </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">RocketFuel</span><span class="pun">({</span><span class="pln"> environment</span><span class="pun">:</span><span class="pln"> </span><span class="str">"&lt;%= developmentEnv %&gt;"</span><span class="pun">,</span><span class="pln"> </span><span class="pun">});</span></code></li><li class="L7"><code><span class="pln">      rkfl</span><span class="pun">.</span><span class="pln">rkflAutoSignUp</span><span class="pun">(</span><span class="pln">payload</span><span class="pun">,</span><span class="pln"> environment </span><span class="pun">=</span><span class="pln"> </span><span class="str">"&lt;%= developmentEnv %&gt;"</span><span class="pun">).</span><span class="kwd">then</span><span class="pun">((</span><span class="pln">res</span><span class="pun">)</span><span class="pln"> </span><span class="pun">=&gt;</span><span class="pln"> </span><span class="pun">{</span></code></li><li class="L8"><code><span class="pln">          </span><span class="com">// save this rkflToken for reference in the DB </span></code></li><li class="L9"><code><span class="pln">          </span><span class="com">// It is unique to each customer</span></code></li><li class="L0"><code><span class="pln">          res</span><span class="pun">.</span><span class="pln">result</span><span class="pun">.</span><span class="pln">rkflToken</span><span class="pun">;</span></code></li><li class="L1"><code><span class="pln">      </span><span class="pun">})})</span></code></li></ol></pre></li><li>Existing RKFL Token<pre class="prettyprint linenums prettyprinted" style=""><ol class="linenums"><li class="L0"><code><span class="pln">      rkfl </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln">  </span><span class="typ">RocketFuel</span><span class="pun">({</span></code></li><li class="L1"><code><span class="pln">          token</span><span class="pun">,</span><span class="pln"> </span><span class="com">// rkfltoken</span></code></li><li class="L2"><code><span class="pln">          uuid</span><span class="pun">,</span></code></li><li class="L3"><code><span class="pln">          callback</span><span class="pun">:</span><span class="pln">  callBackFunc</span><span class="pun">,</span></code></li><li class="L4"><code><span class="pln">          environment</span><span class="pun">:</span><span class="pln">  </span><span class="str">"&lt;%= developmentEnv %&gt;"</span><span class="pln">  </span><span class="com">// prod, preprod</span></code></li><li class="L5"><code><span class="pln">      </span><span class="pun">})</span></code></li></ol></pre><h2 id="h2-you-can-refer-to-the-reference_link-for-demonstration-"><a name="You can refer to the   REFERENCE_LINK  for demonstration." class="reference-link"></a><span class="header-link octicon octicon-link"></span>You can refer to the <a href="https://bitbucket.org/rocketfuelblockchain/rocketfuel-demo-hosted-v2/src/master/">REFERENCE_LINK</a> for demonstration.</h2></li></ul>
</li></ol>
</div></div>
<div class="editormd-container-mask" style="display: none;"></div>
<div class="editormd-mask"></div><div class="editormd-toolbar" style="display: none; position: absolute; width: 100%; left: 0px;"><div class="editormd-toolbar-container"><ul class="editormd-menu"><li><a href="javascript:;" title="Undo(Ctrl+Z)" unselectable="on"><i class="fa fa-undo" name="undo" unselectable="on"></i></a></li><li><a href="javascript:;" title="Redo(Ctrl+Y)" unselectable="on"><i class="fa fa-repeat" name="redo" unselectable="on"></i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="Bold" unselectable="on"><i class="fa fa-bold" name="bold" unselectable="on"></i></a></li><li><a href="javascript:;" title="Strikethrough" unselectable="on"><i class="fa fa-strikethrough" name="del" unselectable="on"></i></a></li><li><a href="javascript:;" title="Italic" unselectable="on"><i class="fa fa-italic" name="italic" unselectable="on"></i></a></li><li><a href="javascript:;" title="Block quote" unselectable="on"><i class="fa fa-quote-left" name="quote" unselectable="on"></i></a></li><li><a href="javascript:;" title="Words first letter convert to uppercase" unselectable="on"><i class="fa" name="ucwords" style="font-size:20px;margin-top: -3px;">Aa</i></a></li><li><a href="javascript:;" title="Selection text convert to uppercase" unselectable="on"><i class="fa fa-font" name="uppercase" unselectable="on"></i></a></li><li><a href="javascript:;" title="Selection text convert to lowercase" unselectable="on"><i class="fa" name="lowercase" style="font-size:24px;margin-top: -10px;">a</i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="Heading 1" unselectable="on"><i class="fa editormd-bold" name="h1" unselectable="on">H1</i></a></li><li><a href="javascript:;" title="Heading 2" unselectable="on"><i class="fa editormd-bold" name="h2" unselectable="on">H2</i></a></li><li><a href="javascript:;" title="Heading 3" unselectable="on"><i class="fa editormd-bold" name="h3" unselectable="on">H3</i></a></li><li><a href="javascript:;" title="Heading 4" unselectable="on"><i class="fa editormd-bold" name="h4" unselectable="on">H4</i></a></li><li><a href="javascript:;" title="Heading 5" unselectable="on"><i class="fa editormd-bold" name="h5" unselectable="on">H5</i></a></li><li><a href="javascript:;" title="Heading 6" unselectable="on"><i class="fa editormd-bold" name="h6" unselectable="on">H6</i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="Unordered list" unselectable="on"><i class="fa fa-list-ul" name="list-ul" unselectable="on"></i></a></li><li><a href="javascript:;" title="Ordered list" unselectable="on"><i class="fa fa-list-ol" name="list-ol" unselectable="on"></i></a></li><li><a href="javascript:;" title="Horizontal rule" unselectable="on"><i class="fa fa-minus" name="hr" unselectable="on"></i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="Link" unselectable="on"><i class="fa fa-link" name="link" unselectable="on"></i></a></li><li><a href="javascript:;" title="Reference link" unselectable="on"><i class="fa fa-anchor" name="reference-link" unselectable="on"></i></a></li><li><a href="javascript:;" title="Image" unselectable="on"><i class="fa fa-picture-o" name="image" unselectable="on"></i></a></li><li><a href="javascript:;" title="Code inline" unselectable="on"><i class="fa fa-code" name="code" unselectable="on"></i></a></li><li><a href="javascript:;" title="Preformatted text / Code block (Tab indent)" unselectable="on"><i class="fa fa-file-code-o" name="preformatted-text" unselectable="on"></i></a></li><li><a href="javascript:;" title="Code block (Multi-languages)" unselectable="on"><i class="fa fa-file-code-o" name="code-block" unselectable="on"></i></a></li><li><a href="javascript:;" title="Tables" unselectable="on"><i class="fa fa-table" name="table" unselectable="on"></i></a></li><li><a href="javascript:;" title="Datetime" unselectable="on"><i class="fa fa-clock-o" name="datetime" unselectable="on"></i></a></li><li><a href="javascript:;" title="Emoji" unselectable="on"><i class="fa fa-smile-o" name="emoji" unselectable="on"></i></a></li><li><a href="javascript:;" title="HTML Entities" unselectable="on"><i class="fa fa-copyright" name="html-entities" unselectable="on"></i></a></li><li><a href="javascript:;" title="Page break" unselectable="on"><i class="fa fa-newspaper-o" name="pagebreak" unselectable="on"></i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="" unselectable="on"><i class="fa fa-terminal" name="goto-line" unselectable="on"></i></a></li><li><a href="javascript:;" title="Unwatch" unselectable="on"><i class="fa fa-eye-slash" name="watch" unselectable="on"></i></a></li><li><a href="javascript:;" title="HTML Preview (Press Shift + ESC exit)" unselectable="on"><i class="fa fa-desktop active" name="preview" unselectable="on"></i></a></li><li><a href="javascript:;" title="Fullscreen (Press ESC exit)" unselectable="on"><i class="fa fa-arrows-alt" name="fullscreen" unselectable="on"></i></a></li><li><a href="javascript:;" title="Clear" unselectable="on"><i class="fa fa-eraser" name="clear" unselectable="on"></i></a></li><li><a href="javascript:;" title="Search" unselectable="on"><i class="fa fa-search" name="search" unselectable="on"></i></a></li><li class="divider" unselectable="on">|</li><li><a href="javascript:;" title="Help" unselectable="on"><i class="fa fa-question-circle" name="help" unselectable="on"></i></a></li><li><a href="javascript:;" title="About Editor.md" unselectable="on"><i class="fa fa-info-circle" name="info" unselectable="on"></i></a></li></ul></div></div></div>