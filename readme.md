# Http-Shutdown ![](https://img.shields.io/travis/thedillonb/http-shutdown.svg?style=flat-square) ![](https://img.shields.io/npm/v/http-shutdown.svg?style=flat-square) ![](http://img.shields.io/npm/dm/http-shutdown.svg?style=flat-square)
Shutdown a Nodejs HTTP server gracefully by terminating the listening socket, then destroying all keep-alive idle sockets all while allowing in-flight requests to finish. Don't be fooled by other "shutdown" techniques. This solution is complete and takes into account many factors left out by other solutions.

## Usage
There are currently two ways to use this library. The first is explicit wrapping of the `Server` object:

```javascript
// Create the http server
var server = require('http').createServer(function(req, res) {
  res.end('Good job!');
});

// Wrap the server object with additional functionality
server = require('http-shutdown')(server);

// Sometime later, shutdown the server.
server.shutdown(function() {
  console.log('Everything is cleanly shutdown.');
});
```

The second is implicitly adding prototype functionality to the `Server` object:

```javascript
// .extend adds a .withShutdown prototype method to the Server object
var httpShutdown = require('http-shutdown').extend();

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
