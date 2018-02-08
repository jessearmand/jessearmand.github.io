---
layout: post
title: "I'm sold with golang"
date: 2013-06-11
description: "A quick apache benchmark of hello world http server on various platforms."
categories: [http-server-benchmark, go, ruby, python, nodejs, eventmachine, twisted, tornado]
---

While I was waiting for WWDC, I did an apache bench for simple hello world server written in go, ruby's eventmachine, python's tornado and twisted, and the well-known node.js.

    % ab -r -k -n 15000 -c 50 -e ~/Documents/each_platform_results.csv http://localhost:port/

15000 requests and 50 concurrent requests is the only setting that I could test on all platforms without failing tests with an error like this:

    apr_socket_connect(): Operation already in progress (37)

How is the result?

OK, let's start with tornado (I believe it's used by facebook for realtime news feed?). Here's the code:

```python
import tornado.httpserver
import tornado.ioloop
import tornado.web

class MainHandler(tornado.web.RequestHandler):
    @tornado.web.asynchronous
    def get(self):
        self.write("Hello World\n")
        self.finish()

application = tornado.web.Application([
    (r"/", MainHandler),
])

if __name__ == "__main__":
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.listen(8001)
    print("Tornado listening at 8001")
    tornado.ioloop.IOLoop.instance().start()
```

Result:

```
    Server Software:        TornadoServer/3.0.2
    Server Hostname:        0.0.0.0
    Server Port:            8001

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      50
    Time taken for tests:   5.072 seconds
    Complete requests:      15000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    15000
    Total transferred:      3465000 bytes
    HTML transferred:       180000 bytes
    Requests per second:    2957.67 [#/sec] (mean)
    Time per request:       16.905 [ms] (mean)
    Time per request:       0.338 [ms] (mean, across all concurrent requests)
    Transfer rate:          667.21 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       1
    Processing:     6   17   6.0     15      65
    Waiting:        6   17   5.9     15      65
    Total:          7   17   6.0     15      65

    Percentage of the requests served within a certain time (ms)
      50%     15
      66%     16
      75%     16
      80%     17
      90%     27
      95%     30
      98%     35
      99%     38
     100%     65 (longest request)
```

Then... the reputable Twisted:

```python
from twisted.web import server, resource
from twisted.internet import reactor

class HelloResource(resource.Resource):
    isLeaf = True

    def render_GET(self, request):
        request.setHeader("Content-Type", "text/plain")
        return "Hello World\n"

port = 8001
reactor.listenTCP(port, server.Site(HelloResource()))
print "Twisted running at %d" % port
reactor.run()
```

Result:

```
    Server Software:        TwistedWeb/13.0.0
    Server Hostname:        0.0.0.0
    Server Port:            8001

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      50
    Time taken for tests:   5.893 seconds
    Complete requests:      15000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    0
    Total transferred:      2115000 bytes
    HTML transferred:       180000 bytes
    Requests per second:    2545.19 [#/sec] (mean)
    Time per request:       19.645 [ms] (mean)
    Time per request:       0.393 [ms] (mean, across all concurrent requests)
    Transfer rate:          350.46 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.2      0       2
    Processing:    11   20   2.2     19      34
    Waiting:       11   20   2.2     19      34
    Total:         11   20   2.2     19      34

    Percentage of the requests served within a certain time (ms)
      50%     19
      66%     20
      75%     20
      80%     21
      90%     22
      95%     23
      98%     25
      99%     27
     100%     34 (longest request)
```

What about ruby eventmachine?

```ruby
require 'eventmachine'
require 'evma_httpserver'

class Handler  < EventMachine::Connection
  include EventMachine::HttpServer

  def process_http_request
    resp = EventMachine::DelegatedHttpResponse.new( self )
    resp.status = 200
    resp.content = "Hello World!"
    resp.send_response
  end
end

EventMachine::run {
  EventMachine::start_server("0.0.0.0", 8002, Handler)
  puts "Listening... at 8002"
}
```

Result:

```
      Server Software:
      Server Hostname:        0.0.0.0
      Server Port:            8002

      Document Path:          /
      Document Length:        12 bytes

      Concurrency Level:      50
      Time taken for tests:   1.909 seconds
      Complete requests:      15000
      Failed requests:        0
      Write errors:           0
      Keep-Alive requests:    0
      Total transferred:      780000 bytes
      HTML transferred:       180000 bytes
      Requests per second:    7858.99 [#/sec] (mean)
      Time per request:       6.362 [ms] (mean)
      Time per request:       0.127 [ms] (mean, across all concurrent requests)
      Transfer rate:          399.09 [Kbytes/sec] received

      Connection Times (ms)
                    min  mean[+/-sd] median   max
      Connect:        0    0   4.4      0     245
      Processing:     1    6  19.5      4     247
      Waiting:        1    5  19.3      3     247
      Total:          2    6  20.0      4     247

      Percentage of the requests served within a certain time (ms)
        50%      4
        66%      4
        75%      4
        80%      4
        90%      6
        95%      8
        98%     11
        99%     80
       100%    247 (longest request)
```

Wow, that's faster than tornado or twisted in terms of requests per second, but it transferred less data for some reason. OK, we move on to node.js, I expect it should be faster.

```javascript
var http = require('http');

http.createServer(function (request, response) {
  response.writeHead(200, { "Content-Type": "text/plain" });
  response.write("Hello World\n");
  response.end();
}).listen(8000);

console.log('Listening on port 8000...');
```

Result:

```
    Server Software:
    Server Hostname:        0.0.0.0
    Server Port:            8000

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      50
    Time taken for tests:   2.181 seconds
    Complete requests:      15000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    0
    Total transferred:      1695000 bytes
    HTML transferred:       180000 bytes
    Requests per second:    6877.80 [#/sec] (mean)
    Time per request:       7.270 [ms] (mean)
    Time per request:       0.145 [ms] (mean, across all concurrent requests)
    Transfer rate:          758.98 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   1.1      0     137
    Processing:     1    7  11.9      6     142
    Waiting:        1    7  11.8      6     142
    Total:          2    7  11.9      6     142

    Percentage of the requests served within a certain time (ms)
      50%      6
      66%      6
      75%      6
      80%      6
      90%      7
      95%      9
      98%     12
      99%    101
     100%    142 (longest request)
```

Yes, unsurprisingly it has the highest transfer rate, although it has less mean requests / sec compared to ruby event machine. Based on the percentage of the requests served, node.js is faster.

Are we done? No, the last but not least:

```go
package main

import (
  "fmt"
  "io"
  "net/http"
  "runtime"
)

func main() {
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "text/plain")
    io.WriteString(w, "Hello World\n")
  })

  runtime.GOMAXPROCS(4)
  fmt.Println("Server running at http://127.0.0.1:3000/ with GOMAXPROCS(4) on i7")
  http.ListenAndServe("127.0.0.1:3000", nil)
}
```

Result:

```
    Server Software:
    Server Hostname:        0.0.0.0
    Server Port:            3000

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      50
    Time taken for tests:   0.228 seconds
    Complete requests:      15000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    15000
    Total transferred:      2070000 bytes
    HTML transferred:       180000 bytes
    Requests per second:    65771.30 [#/sec] (mean)
    Time per request:       0.760 [ms] (mean)
    Time per request:       0.015 [ms] (mean, across all concurrent requests)
    Transfer rate:          8863.71 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       2
    Processing:     0    1   0.1      1       2
    Waiting:        0    1   0.1      1       2
    Total:          0    1   0.2      1       3

    Percentage of the requests served within a certain time (ms)
      50%      1
      66%      1
      75%      1
      80%      1
      90%      1
      95%      1
      98%      1
      99%      1
     100%      3 (longest request)
```

Transfer rate is 8863.71 Kbytes/sec with 65771.3 requests per second?

Go lang, I'm sold.
