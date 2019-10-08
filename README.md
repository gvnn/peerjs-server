[![Build Status](https://travis-ci.org/peers/peerjs-server.png?branch=master)](https://travis-ci.org/peers/peerjs-server)

# PeerServer: A server for PeerJS #

This fork of peerjs-server adds functionality to set a custom ID generation fucntion. [Commit](https://github.com/ajmar/peerjs-server/commit/2552e9d)

PeerServer helps broker connections between PeerJS clients. Data is not proxied through the server.

## [https://peerjs.com](https://peerjs.com)

### Run PeerServer

1. Clone app:
```bash
git clone https://github.com/peers/peerjs-server.git
```

2. Install dependencies:
```bash
npm install
```

3. Run the server:

```bash
$> peerjs --port 9000 --key peerjs --path /myapp
```

Or, create a custom server:

```bash
$> npm install peerjs-server
```

```javascript
import {PeerServer} from 'peerjs-server';

const server = PeerServer({port: 9000, path: '/myapp'});
```

Connecting to the server from PeerJS:

```html
<script>
    const peer = new Peer('someid', {host: 'localhost', port: 9000, path: '/myapp'});
</script>
```

Using HTTPS: Simply pass in PEM-encoded certificate and key.

```javascript
import fs from 'fs';
import {PeerServer} from 'peerjs-server';

const server = PeerServer({
  port: 9000,
  ssl: {
    key: fs.readFileSync('/path/to/your/ssl/key/here.key'),
    cert: fs.readFileSync('/path/to/your/ssl/certificate/here.crt')
  }
});
```

#### Running PeerServer behind a reverse proxy

Make sure to set the `proxied` option, otherwise IP based limiting will fail.
The option is passed verbatim to the
[expressjs `trust proxy` setting](http://expressjs.com/4x/api.html#app-settings)
if it is truthy.

```javascript
import {PeerServer} from 'peerjs-server';

const server = PeerServer({port: 9000, path: '/myapp', proxied: true});
```

### Combining with existing express app

```javascript
import express from 'express';
import {ExpressPeerServer} from 'peerjs-server';

const app = express();
app.get('/', (req, res, next) => { res.send('Hello world!'); });

// =======

const server = app.listen(9000);

const options = {
    debug: true
}

const peerserver = ExpressPeerServer(server, options);

app.use('/api', peerserver);

// == OR ==

import http from 'http';

const server = http.createServer(app);
const peerserver = ExpressPeerServer(server, options);

app.use('/peerjs', peerserver);

server.listen(9000);

// ========
```

### Events

The `'connection'` event is emitted when a peer connects to the server.

```javascript
peerserver.on('connection', (client) => { ... });
```

The `'disconnect'` event is emitted when a peer disconnects from the server or
when the peer can no longer be reached.

```javascript
peerserver.on('disconnect', (client) => { ... });
```

## Running tests

```bash
npm test
```

## Docker

You can build this image simply by calling:
```bash
docker build -t peerjs https://github.com/peers/peerjs-server.git
```

To run the image execute this:  
```bash
docker run -p 9000:9000 -d peerjs
```

This will start a peerjs server on port 9000 exposed on port 9000.

## Problems?

Discuss PeerJS on our Google Group:
https://groups.google.com/forum/?fromgroups#!forum/peerjs

Please post any bugs as a Github issue.
