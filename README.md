[![Build Status](https://api.travis-ci.org/igm/sockjs-go.svg?branch=master)](https://travis-ci.org/igm/sockjs-go) [![GoDoc](http://godoc.org/github.com/igm/sockjs-go/sockjs?status.png)](http://godoc.org/github.com/igm/sockjs-go/sockjs) [![Coverage Status](https://coveralls.io/repos/igm/sockjs-go/badge.png?branch=master)](https://coveralls.io/r/igm/sockjs-go?branch=master)

What is SockJS?
=

SockJS is a JavaScript library (for browsers) that provides a WebSocket-like
object. SockJS gives you a coherent, cross-browser, Javascript API
which creates a low latency, full duplex, cross-domain communication
channel between the browser and the web server, with WebSockets or without.
This necessitates the use of a server, which this is one version of, for GO.


SockJS-Go server
=

SockJS-Go is a [SockJS](https://github.com/sockjs/sockjs-client) server written in Go.

To install **latest stable**(to be deprecated soon) version of `sockjs-go` run:

    go get gopkg.in/igm/sockjs-go.v1/sockjs

To install **v2** of `sockjs-go` run (available soon)

    go get gopkg.in/igm/sockjs-go.v2/sockjs

To install **development** version of `sockjs-go` run:

    go get github.com/igm/sockjs-go/sockjs


Versioning
-

SockJS-Go project adopted [gopkg.in](http://gopkg.in) approach for versioning.


Live Demo
-

There's a live demo running at [Pivotal Web Services](http://run.pivotal.io). Depending on the URL and port various load ballancers process the request wich results in sockjs choosing different connection method:
* WebSockets: https://sockjs-chat.cfapps.io:4443
* Xhr streaming: https://sockjs-chat.cfapps.io
* Xhr polling: http://sockjs-chat.cfapps.io


Example
-

A simple echo sockjs server:


```go
package main

import (
	"log"
	"net/http"

	"github.com/igm/sockjs-go/sockjs"
)

func main() {
	handler := sockjs.NewHandler("/echo", sockjs.DefaultOptions, echoHandler) 
	log.Fatal(http.ListenAndServe(":8081", handler))
}

func echoHandler(conn sockjs.Conn) {
	for {
		if msg, err := conn.Recv(); err == nil {
			conn.Send(msg)
			continue
		}
		break
	}
}
```


SockJS Protocol Tests Status
-
SockJS defines a set of [protocol tests](https://github.com/sockjs/sockjs-protocol) to quarantee a server compatibility with sockjs client library and various browsers. SockJS-Go server library aims to provide full compatibility, however there are couple of tests that don't and probably will never pass due to reasons explained in table below:


| Failing Test | Explanation |
| -------------| ------------|
| **XhrPolling.test_transport** | does not pass due to a feature in net/http that does not send content-type header in case of StatusNoContent response code (even if explicitly set in the code), [details](https://code.google.com/p/go/source/detail?r=902dc062bff8) |
| **WebSocket.** |  Sockjs Go version supports RFC 6455, draft protocols hixie-76, hybi-10 are not supported |
| **JSONEncoding** | As menioned in [browser quirks](https://github.com/sockjs/sockjs-client#browser-quirks) section: "it's advisable to use only valid characters. Using invalid characters is a bit slower, and may not work with SockJS servers that have a proper Unicode support." Go lang has a proper Unicode support |
| **RawWebsocket.** | The sockjs protocol tests use old WebSocket client library (hybi-10) that does not support RFC 6455 properly |

WebSocket
-
As mentioned above sockjs-go library is compatible with RFC 6455. That means the browsers not supprting RFC 6455 are not supported properly. There are no plans to support draft versions of WebSocket protocol. The WebSocket support is based on [Gorilla web toolkit](http://www.gorillatoolkit.org/pkg/websocket) implementation of WebSocket.

For detailed information about browser versions supporting RFC 6455 see this [wiki page](http://en.wikipedia.org/wiki/WebSocket#Browser_support).
