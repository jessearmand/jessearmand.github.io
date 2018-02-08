---
layout: post
title: "More HTTP server benchmark"
date: 2013-06-13
description: "A quick apache benchmark of hello world http server on various platforms."
categories: [http-server-benchmark, scala, play, scalatra, go, ruby, python, nodejs, eventmachine, twisted, tornado]
---

Following from my last [post](http://jessearmand.github.io/http-server-benchmark/2013/06/11/im-sold-with-golang/) on benchmarking hello world http server on various platforms, I continued with increasing the number of requests and concurrency. In addition, I added [scalatra](http://www.scalatra.org/), [play](http://www.playframework.com/), and [greenlet tornado](https://github.com/mopub/greenlet-tornado) to the list.

What is greenlet? To make it short, it's just like goroutine in golang. What's goroutine? Read about it [here](http://www.golang-book.com/10).

This time, I excluded node.js, twisted, and eventmachine from the list, because apache bench could not finish the benchmark at 100,000 number of requests with 100 multiple requests at a time.

node.js and eventmachine could still perform well at 15000 requests with 100 multiple requests, and the results of both don't have a significant difference compared to the last time I tested it. Beyond that, the test was aborted after 10 failures, with the same error:

    apr_socket_connect(): Operation already in progress (37)

I have also made an upgrade on ab executable to Version 2.3 Revision 1430300. This could be built from the latest httpd source code at version 2.4.4.

That being said, I may try to find out what are the problems in my test setup — Mac OS X 10.8.4, Retina MacBook Pro, Mid 2012 with Quad Core i7 at 2.3 GHz — that fails apache bench to finish it for node.js, twisted, and eventmachine. I figured that to do a full-scale benchmark with apache bench, it needs to be run on a Linux server.

The apache bench command that I was using:

    % ab -r -k -n 100000 -c 100 -e ~/Documents/each_platform_results.csv http://localhost:port/

The source code for greenlet with tornado:

```python
import tornado.httpserver
import tornado.web
from greenlet_tornado import greenlet_asynchronous, greenlet_fetch

class MainHandler(tornado.web.RequestHandler):
    @greenlet_asynchronous
    def get(self):
        self.write("Hello World!")

application = tornado.web.Application([
    (r"/", MainHandler),
])

if __name__ == "__main__":
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.listen(8001)
    print("Tornado listening at 8001")
    tornado.ioloop.IOLoop.instance().start()
```

As you can see, there's not much difference except for the `@greenlet_asynchronous` decorator.

The result is not that different either:
```
    Server Software:        TornadoServer/3.0.2
    Server Hostname:        0.0.0.0
    Server Port:            8001

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   32.202 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    100000
    Total transferred:      23100000 bytes
    HTML transferred:       1200000 bytes
    Requests per second:    3105.36 [#/sec] (mean)
    Time per request:       32.202 [ms] (mean)
    Time per request:       0.322 [ms] (mean, across all concurrent requests)
    Transfer rate:          700.52 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       5
    Processing:     6   32  10.7     29     100
    Waiting:        6   32  10.7     29     100
    Total:         10   32  10.7     29     100

    Percentage of the requests served within a certain time (ms)
      50%     29
      66%     29
      75%     30
      80%     30
      90%     51
      95%     61
      98%     63
      99%     68
     100%    100 (longest request)
```

Actually, tornado without greenlet is slightly faster by 0.01 ms.
```
    Server Software:        TornadoServer/3.0.2
    Server Hostname:        0.0.0.0
    Server Port:            8001

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   31.168 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    100000
    Total transferred:      23100000 bytes
    HTML transferred:       1200000 bytes
    Requests per second:    3208.39 [#/sec] (mean)
    Time per request:       31.168 [ms] (mean)
    Time per request:       0.312 [ms] (mean, across all concurrent requests)
    Transfer rate:          723.77 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       4
    Processing:     7   31  11.0     27     108
    Waiting:        7   31  11.0     27     108
    Total:          8   31  11.0     27     108

    Percentage of the requests served within a certain time (ms)
      50%     27
      66%     28
      75%     28
      80%     29
      90%     50
      95%     59
      98%     63
      99%     73
     100%    108 (longest request)
```

Next up, Scalatra which is a [sinatra](http://www.sinatrarb.com) inspired web framework written in [scala](http://www.scala-lang.org), but it takes much longer to setup compared to its predecessor.

Here's the code:

```scala
package com.jessearmand.helloscala

import org.scalatra._
import scalate.ScalateSupport

class MyScalatraServlet extends HelloScalaAppStack {
  get("/") {
    "Hello World."
  }
}
```

Yes, that simple! Once you set it up with some effort, if you never done any Scala programming.

How does it perform? Roughly, it's twice as fast as python's tornado.

```
    Server Software:        Jetty(8.1.8.v20121106)
    Server Hostname:        0.0.0.0
    Server Port:            8080

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   16.332 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    100000
    Total transferred:      14700000 bytes
    HTML transferred:       1200000 bytes
    Requests per second:    6122.82 [#/sec] (mean)
    Time per request:       16.332 [ms] (mean)
    Time per request:       0.163 [ms] (mean, across all concurrent requests)
    Transfer rate:          878.96 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       4
    Processing:     0   16   4.2     16      79
    Waiting:        0   16   4.2     16      79
    Total:          0   16   4.2     16      80

    Percentage of the requests served within a certain time (ms)
      50%     16
      66%     17
      75%     18
      80%     19
      90%     21
      95%     23
      98%     26
      99%     29
     100%     80 (longest request)
```

Now, let's try Play framework which I guess is the best framework written in Scala to build a web application with non-blocking IO.

```scala
package controllers

import play.api._
import play.api.mvc._

object Application extends Controller {
  def index = Action {
    Ok("Hello World!")
    // Ok(views.html.index("Your new application is ready."))
  }
}
```

Note how I commented out the code responsible for rendering the default index.html view. This is only because I want a pure "Hello World" comparison. Although, I do understand that it's not a comprehensive way to benchmark web apps for what it can do in various other cases. Read [here](http://blog.dustinjuliano.com/2013/05/report-comparative-benchmark-of-nginx.html) for a good example of doing this.

Result on initial launch of the app:

```
    Server Software:
    Server Hostname:        0.0.0.0
    Server Port:            9000

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   6.425 seconds
    Complete requests:      100000
    Failed requests:        10
       (Connect: 5, Receive: 5, Length: 0, Exceptions: 0)
    Write errors:           0
    Keep-Alive requests:    99995
    Total transferred:      11599420 bytes
    HTML transferred:       1199940 bytes
    Requests per second:    15563.80 [#/sec] (mean)
    Time per request:       6.425 [ms] (mean)
    Time per request:       0.064 [ms] (mean, across all concurrent requests)
    Transfer rate:          1763.00 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.9      0     105
    Processing:     2    6   4.9      5      96
    Waiting:        0    6   4.9      5      96
    Total:          2    6   5.1      5     157

    Percentage of the requests served within a certain time (ms)
      50%      5
      66%      6
      75%      7
      80%      8
      90%     11
      95%     15
      98%     22
      99%     27
     100%    157 (longest request)
```

Then, after the first benchmark:

```
    Server Software:
    Server Hostname:        0.0.0.0
    Server Port:            9000

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   3.933 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    100000
    Total transferred:      11600000 bytes
    HTML transferred:       1200000 bytes
    Requests per second:    25422.96 [#/sec] (mean)
    Time per request:       3.933 [ms] (mean)
    Time per request:       0.039 [ms] (mean, across all concurrent requests)
    Transfer rate:          2879.94 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       4
    Processing:     2    4   1.2      3      13
    Waiting:        2    4   1.2      3      13
    Total:          2    4   1.3      3      16

    Percentage of the requests served within a certain time (ms)
      50%      3
      66%      4
      75%      4
      80%      5
      90%      6
      95%      7
      98%      7
      99%      8
     100%     16 (longest request)
```

It seems Play framework takes some time from the initial launch to achieve some kind of a "steady state".

Last, let's do some benchmark with go lang again. This time, I didn't set GOMAXPROCS which sets the maximum number of CPUs that can be executing simultaneously. Thus, I let it use a default value that I don't know of.

Result:

```
    Server Software:
    Server Hostname:        127.0.0.1
    Server Port:            3000

    Document Path:          /
    Document Length:        12 bytes

    Concurrency Level:      100
    Time taken for tests:   2.559 seconds
    Complete requests:      100000
    Failed requests:        0
    Write errors:           0
    Keep-Alive requests:    100000
    Total transferred:      13800000 bytes
    HTML transferred:       1200000 bytes
    Requests per second:    39077.64 [#/sec] (mean)
    Time per request:       2.559 [ms] (mean)
    Time per request:       0.026 [ms] (mean, across all concurrent requests)
    Transfer rate:          5266.32 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:        0    0   0.1      0       4
    Processing:     0    3   0.6      3       6
    Waiting:        0    3   0.6      3       6
    Total:          0    3   0.6      3       6

    Percentage of the requests served within a certain time (ms)
      50%      3
      66%      3
      75%      3
      80%      3
      90%      3
      95%      4
      98%      4
      99%      5
     100%      6 (longest request)
```

Go is definitely still a clear winner with half the speed compared to my last benchmark. Next to Go performance is Play framework.

On a related note, there's an interesting article for Python developers if you're thinking about [migrating to Go](http://blog.repustate.com/migrating-code-from-python-to-golang-what-you-need-to-know/2013/04/23/).
