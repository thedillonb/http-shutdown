# Http-Shutdown [![NPM version][npm-image]][npm-url] [![Build status][travis-image]][travis-url] [![Test coverage][coveralls-image]][coveralls-url]
Shutdown a Nodejs HTTP server gracefully by doing the following:

1. Close the listening socket to prevent new connections
2. Close all idle keep-alive sockets to prevent new requests during shutdown
3. Wait for all in-flight requests to finish before closing their sockets.
4. Profit!

Other solutions might just use `server.close` which only terminates the listening socket and waits for other sockets to close - which is incomplete since keep-alive sockets can still make requests. Or, they may use `ref()/unref()` to simply cause Nodejs to terminate if the sockets are idle - which doesn't help if you have other things to shutdown after the server shutsdown.

`http-shutdown` is a complete solution. It uses idle indicators combined with an active socket list to safely, and gracefully, close all sockets. It does not use `ref()/unref()` but, instead, actively closes connections as they finish meaning that socket 'close' events still work correctly since the sockets are actually closing - you're not just `unref`ing and forgetting about them.

## Installation

```bash
$ npm install http-shutdown
```

## Usage
There are currently two ways to use this library. The first is explicit wrapping of the `Server` object:

```javascript
// Create the http server
var server = require('http').createServer(function(req, res) {
  res.end('Good job!');
});

// Wrap the server object with additional functionality.
// This should be done immediately after server construction, or before you start listening.
// Additional functionailiy needs to be added for http server events to properly shutdown.
server = require('http-shutdown')(server);

// Listen on a port and start taking requests.
server.listen(3000);

// Sometime later... shutdown the server.
server.shutdown(function() {
  console.log('Everything is cleanly shutdown.');
});
```

The second is implicitly adding prototype functionality to the `Server` object:

```javascript
// .extend adds a .withShutdown prototype method to the Server object
require('http-shutdown').extend();

var server = require('http').createServer(function(req, res) {
  res.end('God job!');
}).withShutdown(); // <-- Easy to chain. Returns the Server object

// Sometime later, shutdown the server.
server.shutdown(function() {
  console.log('Everything is cleanly shutdown.');
});
```

## License
Copyright (c) 2015 Dillon Buchanan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.  IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.


[npm-image]: https://img.shields.io/npm/v/http-shutdown.svg?style=flat-square
[npm-url]: https://npmjs.org/package/http-shutdown
[travis-image]: https://img.shields.io/travis/thedillonb/http-shutdown.svg?style=flat-square
[travis-url]: https://travis-ci.org/thedillonb/http-shutdown
[coveralls-image]: https://img.shields.io/coveralls/thedillonb/http-shutdown.svg?style=flat-square
[coveralls-url]: https://coveralls.io/r/thedillonb/http-shutdown
