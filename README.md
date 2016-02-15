# comproDLS SDK for Javascript
The official comproDLS SDK for JavaScript, available for browsers and Node.js backends. It provides JavaScript objects and functions to access comproDLS APIs in your application. 

## Key Concepts

### Javascript Promises
Most of the functions in comproDLS SDK are asynchronous and returns a Javascript promise. We use Q library (https://github.com/kriskowal/q) for promises. If you are not familiar with promises, please go through Q library documentation (http://documentup.com/kriskowal/q/).

### What is Access token?
comproDLS uses a token based authentication mechanism i.e. it allows users to enter user credentials (organisationid, username and password) to obtain a security/access token. Once the token is obtained, this can be used to access protected resources/APIs instead of entering user credentials again. The method to obtain access token is explained in [Usage](https://github.com/manavmanocha/comprodls-sdk/blob/master/README.md#usage) section.

### Access token expiry
Access token is valid for limited time and expires after that time. An expired token can't be used to access protected comproDLS resources/APIs. You need to refresh access token after expiry. The method to refresh access token is explained in [Usage](https://github.com/manavmanocha/comprodls-sdk/blob/master/README.md#usage) section.

## Supported APIs
The SDK currently supports the following APIs:
<table>
  <thead>
    <th>API Name</th>
    <th>API Documenatation</th>
    <th>API Version</th>
  </thead>
  <tbody>
    <tr><td>Auth API</td><td>http://auth.comprodls.com/</td><td>1.0.0</td></tr>
    <tr><td>Product API</td><td>http://product.comprodls.com/</td><td>1.0.0</td></tr>
    <tr><td>Collab API</td><td>http://collab.comprodls.com/</td><td>1.0.0</td></tr>
    <tr><td>Analytics API</td><td>http://analytics.comprodls.com/</td><td>1.0.0</td></tr>
    <tr><td>Activity / Test API</td><td>http://activity.comprodls.com/</td><td>1.0.0</td></tr>
    <tr><td>xAPI</td><td>http://xapi.comprodls.com/</td><td>1.0.0</td></tr>      
  </tbody>
</table>

## Installation
### In Browser
To use the SDK in browser, download latest [comprodls-sdk.js]() and add the following script tag in your HTML page:
```
<script src="{SDK Folder location}/comprodls-sdk.js"></script>
```
### Using Bower
You can also use [Bower](http://bower.io/) to install the SDK by typing the following into a terminal window:
```
bower install comprodls-sdk
```
### In Node.js
The preferred way to install the SDK for Node.js is to use the [npm](https://www.npmjs.com/) package manager for Node.js. Simply type the following into a terminal window:
```
npm install comprodls-sdk
```

## Usage
### Loading the SDK
#### In Browser
Once comprodls-sdk.js is loaded, "comproDLS" package is available in window object itself.
```
var comproDLS = new ComproDLS();
```

However, if you are using AMD modules in your application, following is preferred way to get comproDLS package:
```
define(["comprodls-sdk"], function(ComproDLS) {
    var comproDLS = new ComproDLS();
});
```

#### In Node.js
After you've installed the SDK, you can require the comproDLS package in your application using require():
```
var ComproDLS = require('comprodls-sdk');
var comproDLS = new ComproDLS();
```
### Authentication
Authentication is the first thing you need to do before using any function of comproDLS SDK. Following are the ways to authenticate:

#### authenticateByCredentials
You need to have valid comproDLS user credentials (organisationid, username and password) to use this method. Following is sample code to authenticate using credentials:
```
/***
* Authenticate by Credentials
* Parameters
*      organisationid: org1
*      username: student1
*      password: mypassword
**/
var comproDLS = new ComproDLS();
comproDLS.authenticateByCredentials('org1', 'student1', 'mypassword').then(
    function success(response) {
          var access_token = response.access_token;
          
          //Used to refresh access token
          var refresh_token = response.refresh_token;
          
          //Access token will get expired after this time
          var expires_in = response.expires_in;
    }, 
    function error(errorObject) {
         // If credentials are not valid, 401 error is thrown  
         var statuscode = errorObject.status-code;
         if (statuscode == 401) {
              console.log("Invalid Credentials");
         }
    }
);
```
See [API Documentation]() for detailed information on method parameters, success and error response JSONs.

#### authenticateByToken

If you already have a valid comproDLS access token, you can use this method to authenticate. Following is sample code:
```
/***
* Authenticate by Access Token
* Parameters
*     organisationid: org1
*     accesstoken: YWMtI3GOeNOuEeWy2SMOhopIhgAAAVMHr4jsL3aiSJLPClS3lW8cUrkjbBd2ACk~~7b7c4568-71b1-11e5-b98b-33c9b55e28c2
*     refreshtoken: d212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e20f20283865211dda4c8ff24ed714a33cd5986ef06c0ed703c94dde06549d382bc3eab39f6ee410cf7eaa95a52d28b2fca9e389418878f7dcfee7be
**/
var comproDLS = new ComproDLS();
comproDLS.authenticateByToken('org1', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb',
'd212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e20f20283865211dda4c8ff24ed714a33cd5986ef06c0ed703c94dde06549d382bc3eab39f6ee410cf7eaa95a52d28b2fca9e389418878f7dcfee7be').then(
    function success(response) {
          var access_token = response.access_token;
    }, 
    function error(errorObject) {
        //Do error handling here
    }
);
```
#### authenticateByAPIKey
To-do

### Calling comproDLS Services/APIs
comproDLS SDK provides *request* function to call any comproDLS service/API. Following are some samples for using request function

Sample code to get list of all products of a user:
```
/***
* Get all products of a user using SDK
* Example Parameters
*      method: GET
*      url: users/me/products
*      apiname: PRODUCT      
*      params: {} (Empty object)
**/
comproDLS.request('GET', 'users/me/products', 'PRODUCT', {}).then(
    function success(response) {
        //response is array of products
        var firstProductId = response.data[0]['uuid'];
        var firstProductName = response.data[0]['name'];
    }, 
    function error(errorObject) {
         var statuscode = errorObject.status-code;
         if (statuscode == 425) {
             //Call Refresh Access token function to get a new token
         }
    }
);
```


Sample code to post a message in a product feed:
```
/***
* Posts a message in product feed
* Example Parameters
*      method: POST
*      url: post/products/c4aeb8a4-bc18-11e5-8032-c138b3313eb4
*      apiname: COLLAB      
*      params:  {
*            'post':"This is the text", //body of the post
*            'name':'student1', //name of the user
*            'userid':'7b7c4568-71b1-11e5-b98b-33c9b55e28c2', //userId 
*            'verb':'post'
*        }
**/
comproDLS.request('POST', 'post/products/c4aeb8a4-bc18-11e5-8032-c138b3313eb4', 'COLLAB', {
    'post':"This is the text", //body of the post
    'name':'student1', //name of the user
    'userid':'7b7c4568-71b1-11e5-b98b-33c9b55e28c2', //userId 
    'verb':'post'
}).then(
    function success(response) {
        //response is the content of the post
        var textOfPost = response.data[0]['entity']['content']; 
    }, 
    function error(errorObject) {
        var statuscode = errorObject.status-code;
         if (statuscode == 425) {
             //Call Refresh Access token function to get a new token
         }
    }
);
```

### Refreshing Access token
Access token gets expired after "expires_in" time. You need to refresh access token after this time. Use refreshAccessToken function to refresh an expired access token. 

Please note, if you try to call request function without refreshing access token , you will get 425 error status code.

Following is sample code: 
```
/***
* Refresh access token
**/
comproDLS.refreshAccessToken().then(
    function success(response) {
          //Refreshed access token
          var access_token = response.access_token;
          
          //Access token will be expired after this time
          var expires_in = response.expires_in;
          
          //This can be used later to manually refresh expired token
          var refresh_token = response.refresh_token;
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
);
```

## API in Detail

###authenticateByCredentials
This function is used to authenticate with user credentials(organisationid, username and password). If authenticated successfully, it returns a JSON object containing a valid access token.

#### Parameters
* **organisationid**: comproDLS organisation id e.g. cdev6
* **username**: comproDLS username
* **password**: password

#### Success Response Structure
```
{
"userid":"...",         // comproDLS user id
"name":"...",           // User name
"orgid":"...",          // Organisation id
"expires_in":"...",     // Token expiry time, token gets expired after this time
"access_token":"...",   // Access token
"refresh_token":"..."   // Refresh token, this will be used to refresh access token
}
```
#### Error Response Structure
```
{
"status-code":"",       // HTTP status code
"description":"",       // Description of error
"message":"",           // Error message
}
```
####Example
```
comproDLS.authenticateByCredentials('org1', 'student1', 'mypassword').then(
    function success(response) {
          var access_token = response.access_token;

          //Access token will be expired after this time
          var expires_in = response.expires_in;

          //This can be used later to manually refresh expired token
          var refresh_token = response.refresh_token;
    }, 
    function error(errorObject) {
         // If credentials are not valid, 401 error is thrown  
         var statuscode = errorObject.status-code;
         if (statuscode == 401) {
              console.log("Invalid Credentials");
         }
    }
);
```

###authenticateByToken
If you already have a valid comproDLS access token, use this method to authenticate. If authenticated successfully, it returns a JSON object containing a valid access token. This function will automatically refresh access token if it is expired. 

#### Parameters
* **organisationid**: comproDLS organisation id e.g. cdev6
* **accesstoken**: Access Token
* **refreshtoken**: Refresh token

#### Success Response Structure
```
{
"access_token":"...",   // Access token
"refresh_token":"..."   // Refresh token
}
```
#### Error Response Structure
```
{
"description":"",       // Description of error
"message":"",           // Error message
}
```
####Example
```
comproDLS.authenticateByToken('org1,
'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb'
'd212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e20f20283865211dda4c8ff24ed714a33cd5986ef06c0ed703c94dde06549d382bc3eab39f6ee410cf7eaa95a52d28b2fca9e389418878f7dcfee7be').then(
    function success(response) {
          var access_token = response.access_token;
    }, 
    function error(errorObject) {
         //Do error handling here
    }
);
```

## request
This function is used to make http calls to comproDLS API.

#### Parameters
* **method**: http method (e.g. GET , POST , PUT , DELETE) 
* **url**: url of the API call
* **apiname**: API Name. Valid values are PRODUCT, COLLAB, ANALYTICS, AUTH, XAPI, ACTIVITY.
* **params**: Request body for POST and PUT requests.

#### Success Response Structure
JSON Response of corresponding ComproDLS API.
    
#### Error Response Structure
```
{
"status-code":"",       // HTTP status code
"description":"",       // Description of error
"message":"",           // Error message
}
```

#### Standard Error Status codes
<table>
  <thead>
    <th>Status Code</th>
    <th>Reason</th>
  </thead>
  <tbody>
    <tr><td>400</td><td>Bad Request</td></tr>
    <tr><td>401</td><td>Invalid Authorization Token</td></tr>
    <tr><td>403</td><td>User not authorized</td></tr>
    <tr><td>404</td><td>Resource not found</td></tr>
    <tr><td>425</td><td>Access token expired</td></tr>
  </tbody>
</table>

####Example
```
comproDLS.request('GET', 'users/me/products', 'PRODUCT', {}).then(
    function success(response) {
        //response is array of products
        var firstProductId = response.data[0]['uuid'];
        var firstProductName = response.data[0]['name'];
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
);
```

## refreshAccessToken
This function is used to refresh access token.

#### Success Response Structure
    ```
    {
    "userid":"...",         // comproDLS user id
    "name":"...",           // User name
    "orgid":"...",          // Organisation id
    "expires_in":"...",     // Token expiry time, token gets expired after this time. You need to refresh token after expiry
    "access_token":"...",   // NEW Access token
    "refresh_token":"..."   // NEW Refresh token, this will be used to refresh access token
    }
    ```
    
#### Error Response Structure
```
{
"status-code":"",       // HTTP status code
"description":"",       // Description of error
"message":"",           // Error message
}
```
####Example
```
comproDLS.refreshAccessToken().then(
    function success(response) {
         //New refreshed access token
          var access_token = response.access_token;
          
          //New Access token will be expired after this time
          var expires_in = response.expires_in;
          
          //New refresh token
          var refresh_token = response.refresh_token;
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
```
