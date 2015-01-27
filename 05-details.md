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
This has served as a great origin for adding even more convenient features, especially when coupled with transpiling the code with `6to5`, providing access to many useful parts of ES6.

`Promises` are one such useful feature.
Promises are objects used for deferred and asynchronous computations [[__REF__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)].
They make writing asynchronous code much more favorable and manageable than the callback-heavy alternative, and allow for passing around composable handlers for success and failure states of functions.

Normally when data is passed from a controller to a `Handlebars` view template in Cornerstone, the values are substituted in-place when the template is rendered.
For example:

```js
// someController.js

let someController = {
	...
	someRoute(req, res) {
		res.view({
			foo: "hello",
			bar: "world"
		});
	}
	...
};
```

```handlebars
{{! someController/someRoute.hbs }}

<p>{{foo}}</p>
<p>{{bar}}</p>
```

This results in the following being rendered to the client:

```html
<p>hello</p>
<p>world</p>
```

When one or more of the data values passed to a view template is a promise, however, it initiates the streaming feature.
Consider the following example:

```js
// someController.js

let delayedData = new Promise(function(resolve, reject) {
	// resolves the promise after 1000 ms
	setTimeout(function() {
		resolve("delayed data!");
	}, 1000);
});

let someController = {
	...
	someRoute(req, res) {
		res.view({
			foo: "hello",
			bar: delayedData
		});
	}
	...
};
```

In this situation, `bar` would not actually be rendered to the client.
If `{{bar}}` were to be used in the template anyway, it would just be rendered as `[object Object]`, which is the result of JavaScript coercing a `promise` into a `string`.
Instead what happens is that Cornerstone serves the page before the promise has been resolved (which in this case means before the 1000 ms timeout has finished).

Once the client loads the page, it initiates a web socket connection to the server, using `socket.io` [[__REF__]](http://socket.io/).
There is an authorization handshake stage at the beginning of the socket connection in which Cornerstone establishes a bidirectional mapping between sessions and web socket connections, so that the socket connection of any given client may be accessed within any route handler.
This bidirectional mapping is how Cornerstone knows which socket to send data to for each request.
For each promise, the server binds a one-off listener for web socket connections, which tests for whether the connected client is the same client that is waiting for data.
When this test passes, the server attaches a success handler for each promise that emits the resolved value of the promise to the client, through the socket connection.

> NOTE:
> Once the data gets to the client, they can do whatever the heck they want with it.
> In my test case I have blah blah blah
> btw I can fluff this up by explaining WHY I do some things.
> "this is so that..." etc etc
