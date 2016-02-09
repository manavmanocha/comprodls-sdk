# comproDLS SDK for Javascript
The official comproDLS SDK for JavaScript, available for browsers and Node.js backends. It provides JavaScript objects and functions to access comproDLS Services/APIs in your application. 

## Key Concepts

### Javascript Promises
Most of the functions in comproDLS SDK are asynchronous and returns a javascript promise. We use Q library (https://github.com/kriskowal/q) for promises. If you are not familiar with promises, please go through Q library documentation (http://documentup.com/kriskowal/q/).

### What is Access token?
comproDLS uses a token based authentication mechanism i.e it allows users to enter user credentials (organisationid, username and password) to obtain a secuirty/access token. Once the token is obtained, this can be used to access protected resources/APIs instead of entering user credentials again. The method to obtain access token is explained in Usage section.

### Access token expiry
Access token is valid for limited time and expires after that time. An expired token can not be used to access protected comproDLS resources/APIs. You need to refresh access token after expiry. The method to refresh access token is explained in Usage section.

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
After you've installed the SDK, you can create the comproDLS object as follows:
```
var comproDLS = new comproDLS();
```
#### In Node.js
After you've installed the SDK, you can require the comproDLS package in your application using require():
```
var comproDLS = require('comprodls-sdk');
```
### Get Access token
Use *getAccessToken* function to get a valid access token. Following is sample code:
```
// Load the comproDLS SDK
var comproDLS = require('comprodls-sdk');

//Get access token using SDK
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

### Request API
Use *request* function to call any comproDLS API. 
Following is sample code to get list of all products for a user:
```
// Load the comproDLS SDK
var comproDLS = require('comprodls-sdk');

//GET call to get all products of a user
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


Following is sample code to post a message in a group:
```
// Load the comproDLS SDK
var comproDLS = require('comprodls-sdk');

//Get access token using SDK
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

