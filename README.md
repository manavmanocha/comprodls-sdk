# comproDLS SDK for Javascript

Get started quickly using comproDLS APIs with comproDLS SDK for Javascript. The SDK helps take the complexity out of coding by providing JavaScript objects and functions for using comproDLS services.

## Getting Started
### Installation
#### In the Browser
To use the SDK in the browser, simply add the following script tag to your HTML pages:
```
<script src="..."></script>
```
#### In Node.js
The preferred way to install the SDK for Node.js is to use the [npm](https://www.npmjs.com/) package manager for Node.js. Simply type the following into a terminal window:
```
npm install comprodls-sdk
```
#### Using Bower
You can also use [Bower](http://bower.io/) to install the SDK by typing the following into a terminal window:
```
bower install comprodls-sdk
```


### Loading the SDK
#### In the Browser
After you've installed the SDK, you can create the comproDLS object as follows:
```
var comproDLS = new comproDLS();
```
#### In Node.js
After you've installed the SDK, you can require the comproDLS package in your application using require():
```
var comproDLS = require('comprodls-sdk');
```
## Key Concepts

### Javascript Promises
Most of the functions in comproDLS SDK are asynchronous and return a javascript promise. We use Q library (https://github.com/kriskowal/q) for promises. If you are not familiar with promises, please go through Q library documentation(http://documentup.com/kriskowal/q/).

### What is Access token?
comproDLS uses a token based authentication mechanism i.e it allows users to enter valid credentials (organisationid, username and password) to obtain a secuirty/access token. Once the token is obtained, this can be used to access protected resources/APIs instead of entering user credentials again and again. The method to obtain access token is explained in Usage section.

### Access token expiry
Access token is valid for limited time and expires after that time. An expired token can not be used to access protected comproDLS resources/APIs. You need to refresh access token after expiry. The method to refresh access token is explained in Usage section.

## Usage
### Get Access token
Use *getAccessToken* function to get a valid access token. Following is sample code:
```
// Load the comproDLS SDK
var comproDLS = require('comprodls-sdk');
comproDLS.getAccessToken('org1', 'student1', 'mypassword').then(function success(response) {
}, function error(errorObject) {
});
```
