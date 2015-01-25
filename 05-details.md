## TODO

- detailed walkthrough
- cs uses hbs for templates, with data passed by controller
- when a Promise is present in the data, don't wait for it to resolve
- bind a socket event that waits for the client to connect
- once the client establishes a socket connection, send the data when the promises resolve
- delete the socket connection handler


> Since the request latency itself is not easily controlled, the only sensible option is to limit the number of requests.
> The process outlined above requires `n+1` `HTTP` requests to a web server, where `n` is the number of `AJAX` requests, and naturally `n+1` `HTTP` responses.
> This means that there are `2n+2`


## Details

There are a lot of pieces working together to make this work.
The Cornerstone Node.js framework is itself built on top of another popular framework, `Express`, which gives it a very solid foundation in terms of a powerful web framework.
This has served as a great origin for adding even more convenient features, especially when coupled with transpiling the code with `6to5`, which provides access to many parts of ES6.

`Promises` are one such feature.
Promises are objects used for deferred and asynchronous computations [[__REF__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)].
They make writing asynchronous code much more favorable than the callback-heavy alternative, and allow for passing around composable handlers for success and failure states of functions.
When one of the data values passed from a controller to a `Handlebars` view file
