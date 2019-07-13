![Moleculer logo](http://moleculer.services/images/banner.png)

# moleculer-http-client [![Build Status](https://travis-ci.org/AndreMaz/moleculer-http-client.svg?branch=master)](https://travis-ci.org/AndreMaz/moleculer-http-client)

[WIP] A tiny wrapper around [got](https://github.com/sindresorhus/got) HTTP client that allows Moleculer services to communicate with REST APIs.

## Features

- Make HTTP requests from Actions and Events
- Stream data

## Install
```
npm install moleculer-http-client --save
```
## Usage

### Actions
**Action Example**
```js
const { ServiceBroker } = require("moleculer");
const HTTPClientService = require("../../index");

// Create broker
let broker = new ServiceBroker({
  namespace: 'client',
  nodeID: "namespace"
});

// Create a service
broker.createService({
  name: "http",

  // Load HTTP Client Service  
  mixins: [HTTPClientService],
  
  settings: {
    // Only load HTTP GET action
    httpClient: { includeMethods: ["get"] }
  }
});

// Start server
broker.start().then(() => {
  broker
    // Make a HTTP GET request
    .call("http.get", {
      url: "https://httpbin.org/json",
      opt: { json: true }
    })
    .then(res => broker.logger.info(res.body))
    .catch(error => broker.logger.error(error));
});
```

**Result**
```bash
INFO  http-client/HTTP: => HTTP GET to "https://httpbin.org/json"
INFO  http-client/HTTP: <= HTTP GET to "https://httpbin.org/json" returned with status code 200
INFO  http-client/BROKER: { slideshow: { author: 'Yours Truly', date: 'date of publication', slides: [ [Object], [Object] ], title: 'Sample Slide Show' } }
```

### Events
**Event Example**
```js
const { ServiceBroker } = require("moleculer");
const HTTPClientService = require("../../index");

// Create broker
let broker = new ServiceBroker({
  namespace: "client",
  nodeID: "namespace"
});

// Create a service
broker.createService({
  name: "http",

  // Load HTTP Client Service
  mixins: [HTTPClientService],

  settings: {
    // Only load HTTP GET action
    httpClient: { includeMethods: ["get"] }
  },

  events: {
    // Register an event listener
    async "some.Event"(request) {
      this.logger.info("Caught an Event");
      // Make a request
      const res = await this._get(request.url, request.opt);
      this.logger.info("Printing Payload");
      // Print the incoming payload
      this.logger.info(res.body);
    }
  }
});

// Start server
broker.start().then(() => {
  broker
    // Emit some event
    .emit("some.Event", {
      url: "https://httpbin.org/json",
      opt: { json: true }
    });
});
```

```bash
INFO  http-client/HTTP: Caught an Event
INFO  http-client/HTTP: => HTTP GET to "https://httpbin.org/json"
INFO  http-client/HTTP: <= HTTP GET to "https://httpbin.org/json" returned with status code 200
INFO  http-client/HTTP: Printing Payload
INFO  http-client/HTTP: { slideshow: { author: 'Yours Truly', date: 'date of publication', slides: [ [Object], [Object] ], title: 'Sample Slide Show' } }
```

## Default Configs
```js
module.exports = {
  name: "http",

   // Raw Got Client instance https://github.com/sindresorhus/got#instances
  _client: null,

  /**
   * Default settings
   */
  settings: {
    httpClient: {
      // HTTP methods to include as Moleculer Actions  
      includeMethods: null,

      // Boolean value indicating whether request should be logged or not
      logging: true,

      // Log request function
      logOutgoingRequest: logOutgoingRequest,

      // Log response function
      logIncomingResponse: logIncomingResponse,

      // Format the response
      responseFormatter: "headers", // get only the headers

      // Format the Errors
      errorFormatter: errorFormatter,
    }
  },
};
```

## Actions

## Methods

## Test
```
$ npm test
```

# Contribution

Please send pull requests improving the usage and fixing bugs, improving documentation and providing better examples.

# License
The project is available under the [MIT license](https://tldrlegal.com/license/mit-license).

# Contact
Copyright (c) 2019 André Mazayev
