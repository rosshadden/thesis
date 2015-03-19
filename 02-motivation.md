# Motivation

## \textcolor{red}{TODO}

\singlespacing

- get practical everyday use out of WebSockets
- references
	- browser loading order
	- best practices
		- scripts at `</body>`

--------------------------------

\doublespacing


AJAX calls are made from JavaScript code, which initiate an asynchronous call to a specified endpoint.
Once the data is received, a callback gets executed which has access to the data (or error) returned by the server, as well as some request and response metadata.
While this process is very well accepted and works quite well, there are actually some places where it falls short.

The process begins with the server serving a requested page to a client.
It's not until the client has received the entire response that it starts requesting more content.
Next the browser makes requests for all of the external stylesheets referenced in the `<link>` tags in the `<head>` section of the `HTML` page, and any `<script>` tags that may be there as well.
After these requests have been made (though not necessarily before the responses have been received), browsers make requests for `<script>` and `<img>` tags within the `<body>` section of the page.
It is not until all of these resources have been loaded by the browser that the scripts begin to execute.
It is worth noting that any `<script>` tags in `<head>` would actually execute _before_ the rest of the page has finished loading, however it is a well-established best practice to place all scripts at the end of the `<body>` section [@code-standards, *General Coding Principles*].
This is because developers generally want to execute code only once the entire page has been rendered and its resources fetched, in order to avoid such occurrences as the dreaded "Flash Of Unstyled Content" (FOUC) and other things that negatively impact user experience [@hpws, page 43].

After all external resources have been loaded, the JavaScript is executed and AJAX requests may finally be initiated.
When the web server receives an AJAX request, it performs whatever processing or external calls are necessary, and then returns the response.
This is theoretically a rather large gap in the process, as these external calls are not even initiated until the browser has loaded and parsed all of the above resources and makes requests for more data.
It seems reasonable to surmise that this gap may in fact be mitigated, and therein lies the primary motivation of this study.

Another factor in the above process is that of request latency.
The time it takes for an HTTP message to be sent and received is a well-known bottleneck---one which can really only be abated by obtaining a faster connection to the Internet.
If the number of requests being made could be reduced, however, the total latency would inherently decrease as well.
Of course, the server _could_ just do all of the rendering itself, which would limit the number of requests down to one.
However this is potentially even worse than making too many requests, because the user would see only a blank white page until the server returns a response.


## Cornerstone

Together with colleague Sean Clark, I made a back-end web framework called Cornerstone.
Built on Node.js---the server-side JavaScript platform---Cornerstone makes writing APIs and web servers much more convenient.
It transpiles code written using features from ECMAScript 6 (ES6) to code that can run in current, modern JavaScript environments, such as the v8 JavaScript runtime that powers Node.js.
This means that developers are able to employ the use of syntax and concepts from newer versions of JavaScript that are not necessarily implemented yet in many environments.

Having direct access to a framework like Cornerstone as a laboratory put me in a convenient position for approaching some of the shortcomings discussed above.
Not only could I experiment with implementing useful features, but I could also try them in large-scale applications to see how well they may affect such situations.
This symbiotic relationship between Cornerstone and its features has served as another motivation of this study, as any gains in this territory would directly benefit the framework.
