# Raygun4Node

Raygun.io plugin for Node

## Getting Started
Install the module with: `npm install raygun`

```javascript
var raygun = require('raygun');
var raygunClient = new raygun.Client().init({ apiKey: 'your API key' });
raygunClient.send(theError);

// For express, at the end of the middleware definitions, just above app.listen:
app.use(raygunClient.expressHandler);
```

### Expressjs 4.0 and above

The [Express documentation](http://expressjs.com/guide/error-handling.html) says `Though not strictly required, by convention you define error-handling middleware last, after other app.use() calls`, but that is incorrect. If the `app.use(raygunClient.expressHandler);` call is not immediately before the `app.listen` call, then errors will not be handled by Raygun.

## Documentation

### Sending custom data

You can pass custom data in on the Send() function, as the second parameter. For instance (based off the call in test/raygun_test.js):

```javascript
client.send(new Error(), { 'mykey': 'beta' }, function (response){ });
```

### Sending request data

You can send the request data in the Send() function, as the fourth parameter. For example:
```javascript
client.send(new Error(), {}, function () {}, request);
```

If you want to filter any of the request data then you can pass in an array of keys to filter when
you init the client. For example:
```javascript
var raygun = require('raygun');
var raygunClient = new raygun.Client().init({ apiKey: 'your API key', filters: ['password', 'creditcard'] });
```

### Tags

You can add tags to your error in the Send() function, as the fifth parameter. For example:
```javascript
client.send(new Error(), {}, function () {}, {}, ['custom tag 1', 'important error']);
```

### Unique user tracking

New in 0.4: You can set **raygunClient.user** to a function that returns the user name or email address of the currently logged in user.

An example, using the Passport.js middleware:

```javascript
var raygunClient = new raygun.Client().init({apiKey: "yourkey"});

raygunClient.user = function (req) {
  if (req.user) {
    return req.user.username;
  }
}
```

####raygunClient.user(req)

**Param**: *req*: the current request.
**Returns**: The current user's identifier, or an object that describes the user.

This will be transmitted with each message sent, and a count of affected users will appear on the dashboard in the error group view. If you return an email address, and the user has associated a Gravatar with it, their picture will be also displayed.

If you return an object, it may have any of the following properties (only identifier is required):

`identifier` is the user identifier. This will be used to uniquely identify the user within Raygun. This is the only required parameter, but is only required if you are using user tracking.

`isAnonymous` is a bool indicating whether the user is anonymous or actually has a user account. Even if this is set to true, you should still give the user a unique identifier of some kind.

`email` is the user's email address.

`fullName` is the user's full name.

`firstName` is the user's first or preferred name.

`uuid` is the identifier of the device the app is running on. This could be used to correlate user accounts over multiple machines.

Any other properties will be discarded.

**Note:** setUser deprecated in 0.4

Release 0.3 previously had a setUser function that accepted a string or function to specify the user, however it did not accept arguments. This method is considered deprecated and will be removed in the 1.0 release, thus it is advised to update your code to set it with the new *user* function.

### Version tracking

Call setVersion(*string*) on a RaygunClient to set the version of the calling application. This is expected to be of the format x.x.x.x, where x is a positive integer. The version will be visible in the dashboard.

### Examples
View a screencast on creating an app with Node.js and Express.js, then hooking up the error handling and sending them at [http://raygun.io/blog/2013/07/video-nodejs-error-handling-with-raygun/](http://raygun.io/blog/2013/07/video-nodejs-error-handling-with-raygun/)

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using "npm test".

## Release History
- 0.6.0 - Added ability to send tags with exception reports.
- 0.5.0 - Added filters for sensitive request data, and better affected user tracking
- 0.4.2 - Minor test refactor
- 0.4.1 - Fixed issue where getting cpu information returned undefined
- 0.4.0 - Added *user* function, deprecated setUser
- 0.3.0 - Added version and user tracking functionality; bump jshint version, update test
- 0.2.0 - Added Express handler, bug fixes
- 0.1.2 - Include more error information
- 0.1.1 - Point at the correct API endpoint, include correct dependencies for NPM
- 0.1.0 - Initial release

## License
Copyright (c) 2015 MindscapeHQ
Licensed under the MIT license.
