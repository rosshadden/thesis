## TODO
- improve cs
- get practical everyday use out of web sockets
- close the gap between a page loading, making ajax requests, and getting responses
- good place for references!
	- browser loading order
	- best practices
		- scripts at `</body>`


`AJAX` calls are made from `JavaScript` scripts, which initiate an asynchronous call to a specified endpoint.
Once the data is received, a callback gets executed which has access to the data (or error) returned by the server, as well as some request and response metadata.
While this process is very well accepted and works quite well, there are actually some places where it falls short.

The process begins with the server serving a requested page to a client.
It's not until the client has received the entire response that it starts requesting more content.
Next the browser makes requests for all of the external stylesheets referenced in the `<link>` tags in the `<head>` section of the `HTML` page, and any `<script>` tags that may be there as well.
After these requests have been made (though not necessarily before the responses have been received), browsers make requests for `<script>` and `<img>` tags within the `<body>` section of the page.
It is not until all of these resources have been loaded by the browser that the scripts begin to execute.
It is worth noting that any `<script>` tags in `<head>` would actually execute __before__ the rest of the page has finished loading, however it is a well-established best practice to place all scripts at the end of the `<body>` section [__REF__].
This is because developers generally want to execute code only once the entire page has been rendered and its resources fetched, in order to avoid such occurrences as the dreaded "Flash Of Unstyled Content" (FOUC) and other things that negatively impact user experience [__REF__].

After all external resources have been loaded, the `JavaScript` is executed and `AJAX` requests may finally be initiated.
When the web server receives an `AJAX` request, it performs whatever processing or external calls are necessary, and then returns the response.
This is theoretically a rather large gap in the process, as these external calls are not even initiated until the browser has loaded and parsed all of the above resources and makes requests for more data.
It seems reasonable to surmise that this gap may in fact be mitigated, and therein lies the primary motivation of this study.

Another factor in the above process is that of request latency.
The time it takes for an `HTTP` message to be sent and received is a well-known bottleneck---one which can really only be abated by obtaining a faster connection to the Internet.
Since the request latency is not easily controlled, the only sensible option is to limit the number of requests.

## NOTE: these should go in the /details section
> The process outlined above requires `n+1` `HTTP` requests to a web server, where `n` is the number of `AJAX` requests, and naturally `n+1` `HTTP` responses.
> This means that there are `2n+2`
