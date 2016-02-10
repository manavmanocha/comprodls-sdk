# comproDLS SDK for Javascript
The official comproDLS SDK for JavaScript, available for browsers and Node.js backends. It provides JavaScript objects and functions to access comproDLS APIs in your application. 

## Key Concepts

### Javascript Promises
Most of the functions in comproDLS SDK are asynchronous and returns a javascript promise. We use Q library (https://github.com/kriskowal/q) for promises. If you are not familiar with promises, please go through Q library documentation (http://documentup.com/kriskowal/q/).

### What is Access token?
comproDLS uses a token based authentication mechanism i.e it allows users to enter user credentials (organisationid, username and password) to obtain a secuirty/access token. Once the token is obtained, this can be used to access protected resources/APIs instead of entering user credentials again. The method to obtain access token is explained in [Usage](https://github.com/manavmanocha/comprodls-sdk/blob/master/README.md#usage) section.

### Access token expiry
Access token is valid for limited time and expires after that time. An expired token can not be used to access protected comproDLS resources/APIs. You need to refresh access token after expiry. The method to refresh access token is explained in [Usage](https://github.com/manavmanocha/comprodls-sdk/blob/master/README.md#usage) section.

## Installation
### In Browser
To use the SDK in browser, simply add the following script tag to your HTML pages:
```
<script src="..."></script>
```
### In Node.js
The preferred way to install the SDK for Node.js is to use the [npm](https://www.npmjs.com/) package manager for Node.js. Simply type the following into a terminal window:
```
npm install comprodls-sdk
```
### Using Bower
You can also use [Bower](http://bower.io/) to install the SDK by typing the following into a terminal window:
```
bower install comprodls-sdk
```

## Usage
### Loading the SDK
#### In Browser
If you are using AMD modules in your application, following is preferred way to get comproDLS module:
```
define(["comprodls-sdk"], function(comproDLS) {
    //comproDLS object is avaialable here
});
```
If you are not using AMD modules, "comproDLS" object is avaialable in window itself.
#### In Node.js
After you've installed the SDK, you can require the comproDLS module in your application using require():
```
var comproDLS = require('comprodls-sdk');
```
### Getting Access token
Use *getAccessToken* function to get a access token. Following is sample code:
```
/***
* Get access token using SDK
* Parameters
*      organisationid: org1
*      username: student1
*      password: mypassword
**/
comproDLS.getAccessToken('org1', 'student1', 'mypassword').then(
    function success(response) {
          //You need to persist access_token to use it in subsequent calls
          var access_token = response.access_token;
          
          //Access token will be expired after this time
          var expires_in = response.expires_in;
          
          //This will be used later to refresh expired token
          var refresh_token = response.refresh_token;
         
    }, 
    function error(errorObject) {
         //Do Error handling here
         var statuscode = errorObject.status-code;
         var errormessage = errorObject.message;
         console.log(errormessage);
    }
);
```

Application which is using comproDLS SDK needs to persist this access token as this will be used in subsequent calls to comproDLS services/APIs. See [getAccessToken API Documentation](https://github.com/manavmanocha/comprodls-sdk/blob/master/README.md#getaccesstoken) for detailed information on parameters, response JSON structure.

### Request API
comproDLS SDK exposes a *request* function to call any comproDLS service/API. Following are some samples for using request function

Sample code to get list of all products for a user:
```
/***
* Get all products of a user using SDK
* Example Parameters
*      method: GET
*      url: org1/users/me/products
*      accesstooken: YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb
*      params: {} (Empty object)
**/
comproDLS.request('GET', 'org1/users/me/products', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb', {}).then(
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


Sample code to post a message in a product feed:
```
/***
* Posts a message in product feed
* Example Parameters
*      method: POST
*      url: /org1/post/products/c4aeb8a4-bc18-11e5-8032-c138b3313eb4
*      accesstooken: YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb
*      params:  {
*            'post':"This is the text", //body of the post
*            'name':'student1', //name of the user
*            'userid':'7b7c4568-71b1-11e5-b98b-33c9b55e28c2', //userId 
*            'verb':'post'
*        }
**/
comproDLS.request('POST', '/org1/post/products/c4aeb8a4-bc18-11e5-8032-c138b3313eb4', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb', {
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
         //Do Error handling here
    }
);
```
### Refresh Access token
Access token received via getAccessToken function expires after some time. You need to refresh access token after it gets expired. Following is sample code to refresh access token 
```
/***
* Refresh access token using SDK
* Example Parameters
*      organisationid: org1
*      expiredToken : YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb
*      refreshToken : d212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e271262219611c6a9f4c8ff264a07ec33088a729ed3c28d703c94ddf3f7ec50a1cd08e80b557ea15ab7894a7d0034bfad0cfd6864ab870c7dbf4d398
**/
comproDLS.refreshAccessToken('org1', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb', 'd212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e271262219611c6a9f4c8ff264a07ec33088a729ed3c28d703c94ddf3f7ec50a1cd08e80b557ea15ab7894a7d0034bfad0cfd6864ab870c7dbf4d398').then(
    function success(response) {
         //Persist access_token to use it in subsequent calls
          var access_token = response.access_token;
          
          //Access token will be expired after this time
          var expires_in = response.expires_in;
          
          //This will be used later to refresh expired token
          var refresh_token = response.refresh_token;
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
);
```

## API in Detail

###getAccessToken
This function is used to get access token.
#### Parameters
* organisationid: comproDLS organisation id e.g. cdev6
* username: comproDLS username
* password: password

#### Success Response Structure
```
{
"userid":"...",         // comproDLS user id
"name":"...",           // User name
"orgid":"...",          // Organisation id
"expires_in":"...",     // Token expiry time, token gets expired after this time. You need to refresh token after expiry
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
comproDLS.getAccessToken('org1', 'student1', 'mypassword').then(
    function success(response) {
          var access_token = response.access_token;
          //Persist access_token to use it in subsequent calls
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
);
```

## request
This function is used to make http calls to comproDLS API.

#### Parameters
* method: http method (e.g. "GET") 
* url: url of the API call
* accesstoken: comproDLS access token 
* params: entity body for PATCH, POST and PUT requests.

#### Success Response Structure
    This is the response body depending on the API call.
    
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
comproDLS.request('GET', 'org1/users/me/products', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb', {}).then(
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
 This function is used to get referesh access token after the access token has expired.
 
 #### Parameters
* organisationid: comproDLS organisation id e.g. cdev6
* expiredToken : old comproDLS access token value
* refreshToken : old comproDLS refresh token value

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
comproDLS.refreshAccessToken('org1', 'YWMta_x4GM8eEeWRyiUErvtXNwAAAVLpysmC7z_1zk1Fp6MF4nIQGafognqPeVE~~bc611904-a47f-11e5-bee2-930f4cf5acfb', 'd212ca7c0926729a227aa3e26b1e11f4d6a70050378fb8e271262219611c6a9f4c8ff264a07ec33088a729ed3c28d703c94ddf3f7ec50a1cd08e80b557ea15ab7894a7d0034bfad0cfd6864ab870c7dbf4d398').then(
    function success(response) {
         //Persist access_token to use it in subsequent calls
          var access_token = response.access_token;
          
          //Access token will be expired after this time
          var expires_in = response.expires_in;
          
          //This will be used later to refresh expired token
          var refresh_token = response.refresh_token;
    }, 
    function error(errorObject) {
         //Do Error handling here
    }
```
