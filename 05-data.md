# Data

At its core, the testing performed is done so in an attempt to find the fastest or best available method for serving dynamic web pages to users.
In most of the test scenarios, these pages contain data populated from external requests.
The data can be anything from returning a value after a timeout to downloading and serving the entire text of a novel.
The need to deliver data to a client is a common one, and these tests were carried out with that in mind.


## Methods

All of the testing scenarios executed are broken up into three different testing methods.
These will henceforth be referred to as the "server", "AJAX", and "stream" workflows.


### Server

The first of these methods, the server workflow, is to have the server carry out all external requests.
After receiving a request from a client to load an endpoint that requires additional data, the server makes the necessary requests.
Once all of the relevant data has been fetched, it serves the page to the client the requested it, with all pertinent data included within the markup.
Such a workflow is not always applicable in web applications, especially for those with dynamic content, however this is nonetheless a common and mature procedure for delivering data to clients.
Thus this is meant to be more of a control case, against which the other two methods may be benchmarked.


### AJAX

The AJAX workflow is perhaps currently the most common standard for websites serving content any more elaborate than simple static markup.
When an endpoint receives a request from a client that requires additional data, it serves back minimal markup without the data present.
The client then makes AJAX calls to request the rest of the data.
As the client receives the responses for this additional data, it does what it needs to do with it (which is in this case render it to the page and perform the necessary testing calculations).


### Stream

The stream workflow employs the process detailed earlier in this paper, which is theoretically a middle-ground between the two aforementioned workflows, with the advantages of AJAX and the speed of the server workflow.
In terms of the testing methods, the web server makes requests for any external data as soon as a client hits an endpoint, not unlike the aforementioned server workflow.
The server then immediately responds to the client with minimal markup without the data present, not unlike the above AJAX workflow.
As the server receives the responses for the additional data, it sends it to the client via an established WebSocket connection.


## Tests

The tests carried out are enacted in such a way as to simulate common scenarios encountered in typical web applications.

The independent variable in these tests is the time between an endpoint receiving an initial request for a page, and requests for external data.
Essentially, the time until requests for data are made is intentionally altered.
The dependent variable is the time between an endpoint receiving an initial request for a page, and a client receiving all of the data it needs.
This is the time it takes for a client to be completely finished with its interactions with the server.
Each individual scenario may also contain several variants, in which a secondary variable is altered in an attempt to establish trends within the scenario.

The way the dependent variable is measured is similar between each testing method.
When an endpoint receives a request from a client, it immediately saves a reference to `Date.now()`, which "return[s] a `Number` value that is the time value designating the UTC date and time of the occurrence of the call to `now`," where the time value "is measured in `ECMAScript` in milliseconds since 01 January, 1970 UTC" [@ecma, section 15.9.4.4].
This `start` value is passed along with the markup for the pages served in each workflow.
Once each testing method is complete, it calls a function `CS.diff`, which records a new value of `Date.now()`, and measures the difference between the two time values.
The only part different between workflows is at what point the process is considered to be "complete".
For the server workflow, `CS.diff()` is run as soon as the browser executes the JavaScript code on the served page.
The AJAX workflow runs `CS.diff()` once it receives all of the responses it is expecting for the various testing scenarios.
The stream workflow does the same thing as the AJAX one, though naturally it receives its data through a different transport.
### Local file

\textcolor{red}{Blah}.


### Remote file

\textcolor{red}{Blah}.


### Local database

\textcolor{red}{Blah}.


### Remote database

\textcolor{red}{Blah}.


### Timeout

In the timeout scenario, the server sends responses to the client after a (varying) timeout.
When a client hits the endpoint, the server runs a `setTimeout`, after which the response is resolved.
Unlike the other scenarios, this is a largely contrived workflow on its own, however it is meant to simulate the often asynchronous nature of web applications.

\pgfplotstableread{data/timeout.dat} \dataTimeout

\begin{figure}[h!]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time it takes to load things with `setTimeout`},
			% axes
			xlabel={Timeout},
			xticklabels from table={\dataTimeout}{timeout},
			x unit=\si{\ms},
		]

			% server
			\addplot+[
				smooth,
				color=red,
				mark=square
			]
				table[y = server] from \dataTimeout;
			\addlegendentry{Server}

			% ajax
			\addplot+[
				smooth,
				color=blue,
				mark=triangle
			]
				table[y = ajax] from \dataTimeout;
			\addlegendentry{AJAX}

			% stream
			\addplot+[
				smooth,
				color=violet,
				mark=o
			]
				table[y = stream] from \dataTimeout;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Timeout workflow data.}
\end{figure}


