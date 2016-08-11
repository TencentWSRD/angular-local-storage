angular-local-storage-ci-dev
=====================
An Angular module that gives you access to the browsers local storage

A forked version for our project CI, add new features such as expiry support for localStorage and so on to meet our demand.

## Install
```bash
$ npm install angular-local-storage-ci-dev
```

```javascript
var angularLocalStorage = require('angular-local-storage-ci-dev');

angular.module('app', [
    angularLocalStorage
  ]);
```
##Configuration
###setPrefix
You could set a prefix to avoid overwriting any local storage variables from the rest of your app<br/>
**Default prefix:** `ls.<your-key>`
```js
myApp.config(function (localStorageServiceProvider) {
  localStorageServiceProvider
    .setPrefix('yourAppName');
});
```
###setStorageType
You could change web storage type to localStorage or sessionStorage<br/>
**Default storage:** `localStorage`
```js
myApp.config(function (localStorageServiceProvider) {
  localStorageServiceProvider
    .setStorageType('sessionStorage');
});
```
###setStorageCookie
Set cookie options (usually in case of fallback)<br/>
**cookieOptions:** options
**cookieOptions.path** the web path the cookie represents. **default:** `'/'`
```js
myApp.config(function (localStorageServiceProvider) {
  localStorageServiceProvider
    .setStorageCookie(45, '<path>');
});
```

###setNotify

Configure whether events should be broadcasted on $rootScope for each of the following actions:<br/>
**setItem** , default: `true`, event "LocalStorageModule.notification.setitem"<br/>
**removeItem** , default: `false`, event "LocalStorageModule.notification.removeitem"
```js
myApp.config(function (localStorageServiceProvider) {
  localStorageServiceProvider
    .setNotify(true, true);
});
```
###Configuration Example
Using all together
```js
myApp.config(function (localStorageServiceProvider) {
  localStorageServiceProvider
    .setPrefix('newci')
    .setStorageType('sessionStorage')
    .setNotify(true, true)
});
```
##API Documentation
##isSupported
Checks if the browser support the current storage type(e.g: `localStorage`, `sessionStorage`).  
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  if(localStorageService.isSupported) {
    //...
  }
  //...
});
```
###getStorageType
**Returns:** `String`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  var storageType = localStorageService.getStorageType(); //e.g localStorage
  //...
});
```
###set
Directly adds a value to local storage.<br/>
If local storage is not supported, use cookies instead.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function submit(key, val) {
   // expiry: milliseconds, this value will be expired in `expiry` milliseconds, if not set, and `localStorageService.expiry.alwaysExpire` is true, it will expire in the default expiry time.
   // expireTimeStamp: TimeStamp, this value will be expired after this absolute time.
   return localStorageService.set(key, val, expiry, expireTimeStamp);
  }
  //...
});
```
###get
Directly get a value from local storage.<br/>
If local storage is not supported, use cookies instead.<br/>
**Returns:** `value from local storage`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function getItem(key) {
   // expiry: Number, if this value hasn't been wrapped by us, we will wrap them by using this expiry time.
   return localStorageService.get(key, expiry);
  }
  //...
});
```
###keys
Return array of keys for local storage, ignore keys that not owned.<br/>
**Returns:** `value from local storage`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  var lsKeys = localStorageService.keys();
  //...
});
```
###remove
Remove an item(s) from local storage by key.<br/>
If local storage is not supported, use cookies instead.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function removeItem(key) {
   return localStorageService.remove(key);
  }
  //...
  function removeItems(key1, key2, key3) {
   return localStorageService.remove(key1, key2, key3);
  }
});
```
###clearAll
Remove all data for this app from local storage.<br/>
If local storage is not supported, use cookies instead.<br/>
**Note:** Optionally takes a regular expression string and removes matching.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function clearNumbers(key) {
   return localStorageService.clearAll(/^\d+$/);
  }
  //...
  function clearAll() {
   return localStorageService.clearAll();
  }
});
```
###bind
Bind $scope key to localStorageService.  
**Usage:** `localStorageService.bind(scope, property, value[optional], key[optional])`  
***key:*** The corresponding key used in local storage  
**Returns:** deregistration function for this listener.
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  localStorageService.set('property', 'oldValue');
  $scope.unbind = localStorageService.bind($scope, 'property');

  //Test Changes
  $scope.update = function(val) {
    $scope.property = val;
    $timeout(function() {
      alert("localStorage value: " + localStorageService.get('property'));
    });
  }
  //...
});
```
```html
<div ng-controller="MainCtrl">
  <p>{{property}}</p>
  <input type="text" ng-model="lsValue"/>
  <button ng-click="update(lsValue)">update</button>
  <button ng-click="unbind()">unbind</button>
</div>
```

###deriveKey
Return the derive key  
**Returns** `String`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  localStorageService.set('property', 'oldValue');
  //Test Result
  console.log(localStorageService.deriveKey('property')); // ls.property
  //...
});
```
###length
Return localStorageService.length, ignore keys that not owned.  
**Returns** `Number`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  var lsLength = localStorageService.length(); // e.g: 7
  //...
});
```
##Cookie
Deal with browser's cookies directly.
##cookie.isSupported
Checks if cookies are enabled in the browser.  
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  if(localStorageService.cookie.isSupported) {
    //...
  }
  //...
});
```
###cookie.set
Directly adds a value to cookies.<br/>
**Note:** Typically used as a fallback if local storage is not supported.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function submit(key, val) {
   return localStorageService.cookie.set(key, val);
  }
  //...
});
```
**Cookie Expiry** Pass a third argument to specify number of days to expiry
```js
    localStorageService.cookie.set(key,val,10)
```
sets a cookie that expires in 10 days.
###cookie.get
Directly get a value from a cookie.<br/>
**Returns:** `value from local storage`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function getItem(key) {
   return localStorageService.cookie.get(key);
  }
  //...
});
```
###cookie.remove
Remove directly value from a cookie.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function removeItem(key) {
   return localStorageService.cookie.remove(key);
  }
  //...
});
```
###cookie.clearAll
Remove all data for this app from cookie.<br/>
**Returns:** `Boolean`
```js
myApp.controller('MainCtrl', function($scope, localStorageService) {
  //...
  function clearAll() {
   return localStorageService.cookie.clearAll();
  }
});
```
