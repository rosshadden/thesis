# Details

There are a lot of pieces working together to make this work.
The Cornerstone Node.js framework is itself built on top of another popular framework, Express, which gives it a very solid foundation in terms of a powerful web framework.
This has served as a great origin for adding even more convenient features, especially when coupled with transpiling the code with 6to5, providing access to many useful parts of ES6.

Promises are one such useful feature.
Promises are objects used for deferred and asynchronous computations [[__REF__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)].
They make writing asynchronous code much more favorable and manageable than the callback-heavy alternative, and allow for passing around composable handlers for success and failure states of functions.


## Process

Normally when data is passed from a controller to a Handlebars view template in Cornerstone, the values are substituted in-place when the template is rendered.
For example:

```js
// someController.js

let someController = {
	// ...
	someRoute(req, res) {
		res.view({
			foo: "hello",
			bar: "world"
		});
	}
	// ...
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
	// resolves the promise after 5000 ms
	setTimeout(function() {
		resolve("delayed data!");
	}, 5000);
});

let someController = {
	// ...
	someRoute(req, res) {
		res.view({
			foo: "hello",
			bar: delayedData
		});
	}
	// ...
};
```

In this situation, `bar` would not actually be rendered to the client.
If `{{bar}}` were to be used in the template anyway, it would just be rendered as `[object Object]`, which is the result of JavaScript coercing a `Promise` into a `String`.
Instead what happens is that Cornerstone serves the page before the promise has been resolved (which in this case means before the \SI{5000}{\ms} timeout has finished).

Once the client loads the page, it initiates a WebSocket connection to the server, using socket.io [[__REF__]](http://socket.io/).
There is an authorization handshake stage at the beginning of the socket connection in which Cornerstone establishes a bidirectional mapping between sessions and WebSocket connections, so that the socket connection of any given client may be accessed within any route handler.
This bidirectional mapping is how Cornerstone knows which socket to send data to for each request.
It also provides a way to access a client's session data from the scope of a socket connection, though that functionality does not come to play within this workflow.
For each promise, the server binds a one-off listener for WebSocket connections, which tests for whether the connected client is the same client that is waiting for data.
When this test passes, the server attaches a success handler for each promise that emits the resolved value of the promise to the client, through the socket connection.
It then destroys the listener, which has at this point served its intended purpose and is no longer necessary.

Once the data gets to the client, a developer has several options on how to use the data.
Since the data is just being sent through a WebSocket connection, the communication events may be bound to by the client in JavaScript, which provides access to the raw data being sent by the server.
This is the most powerful option, as the client can do whatever it wants with the data.

Another method of using the data sent via the WebSocket transport is to use Cornerstone's `{{{stream}}}` Handlebars helper.
This helper is very simple in implementation.
It just returns a `<var>` HTML tag with a `data-promise` attribute set to the key of the promise.

For example, given the situation outlined above where `bar` is a promise being passed to a view, consider a view file with the following markup:

```handlebars
<p>{{{stream "bar"}}}</p>
```

The above view markup would be rendered and served to the client as the following HTML:

```html
<p><var data-promise="bar"></var></p>
```

The purpose of the `<var>` tags is to serve as a placeholder for the deferred data to be rendered in.
When a client receives a socket broadcast with the data for `bar`, it replaces this placeholder with the received data.
The way that the placeholder replacement works is by using the key sent in the WebSocket broadcast to select the `<var>` tag with the matching `data-promise` attribute.
This HTML-output method does not work well for all situations, of course, but when HTML or raw data needs to be displayed it is rather convenient.
