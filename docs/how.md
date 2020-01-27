# How sttp client works

## Describe the request

This first step when using sttp client is describing the request that you'd like to send. 

A request is represented as an immutable data structure of type `RequestT` (as in Request Template). The basic request is provided as the `basicRequest` value, in the `sttp.client` package. It can be refined using one of the available methods, such as `.header`, `.body`, `.get(Uri)` or `.responseAs`.

A `RequestT` value contains both information on what to include in the request, but also how to handle the response body. 

To start describing a request, import the sttp client package and customise `basicRequest`:

```scala
import sttp.client._
val myRequest = basicRequest.(...)
```

## Send the request

Once the request is described as a value, it can be sent. To send a request, you'll need to have an implicit value of type `SttpBackend` in scope. 

The backend is where most of the work happens: the request is translated to a backend-specific form; a connection is opened, data sent and received; finally, the backend-specific response is translated to sttp's `Response`, as described in the request.

A backend can be synchronous, that is, sending a request can be a blocking operation. When invoking `myRequest.send()`, you'll get a value of type `Response[T]`. Backends can also be asynchronous, and evaluate the send operation eagarly or lazily. For example, when using the [akka-http backend](backends/akkahttp.html), `myRequest.send()` will return a `Future[Response[T]]`: an eagerly-evaluated, asynchronous result. When using the Monix [async-http-client backend](backends/asynchttpclient.html), you'll get back a `Task[Response[T]]`: a lazily-evaluated, but also non-blocking and asynchronous result. 

Backends manage the connection pool, thread pools for handling responses, depending on the implementation provide various configuration options, and optionally support [streaming](requests/streaming.html) and [websockets](requests/websockets.html). They typically need to be created upon application startup, and closed when the application terminates. 

For example, the following sends a synchronous request, using the default JVM backend:

```scala
implicit val backend = HttpURLConnectionBackend()
val response = myRequest.send()
```

## Next steps

Read more about:

* [describing the request](requests/basics.html)
* the [`RequestT` type](requests/type.html)
* specifying how to handle the [response body](responses/body.html)
* [available backends](backends/summary.html)