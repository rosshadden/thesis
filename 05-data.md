# Data

At its core, the testing performed is done in an attempt to find the fastest or best available method for serving dynamic web pages to users.
In most of the test scenarios, these pages contain data populated from external requests, where the client makes a request to the server for data.
The data can be anything from a simple boolean or numeric value, to the contents of an immense number of documents from a database.
The need to deliver data to a client is a common one, and these tests were carried out with that in mind.


## Methods

All of the testing scenarios executed are broken up into three different testing methods.
These will henceforth be referred to as the "server", "AJAX", and "stream" workflows.


### Server

The first of these methods, the server workflow, is implemented by having the server carry out all ancillary requests itself.
After receiving a request from a client to load an endpoint that requires additional data, the server makes all of the necessary requests.
Once all of the relevant data has been fetched, it serves the page to the client that requested it, with all pertinent data already included within the markup.
Such a workflow is not always applicable in web applications---especially for those with dynamic content---however this is nevertheless both a common and mature procedure for delivering data to clients.
The longer the server takes to serve a page, the longer the user's client remains in a vestigial pending state.
Thus the server workflow is meant to be more of a control case, against which the other two methods may be benchmarked in a recognizable fashion.


### AJAX

The AJAX workflow is arguably the most common standard for websites serving content any more elaborate than simple static markup.
When an endpoint receives a request from a client that requires additional data, it serves back minimal markup without the data present.
The client then makes AJAX calls to request the ancillary data.
As the client receives the responses for this additional data, it does what it needs to do with it (which is in this case render it to the page and perform the necessary testing calculations).


### Stream

The stream workflow employs the process detailed earlier in this paper, which is theoretically a middle-ground between the two aforementioned workflows, with the advantages of AJAX and the speed of the server workflow.
In this process, the web server makes requests for any external data as soon as a client hits an endpoint, not unlike the aforementioned server workflow.
The server then immediately responds to the client with minimal markup without the data present, which is more akin to the above AJAX workflow.
As the server receives the responses for the additional data, it sends it to the client through an established WebSocket connection.


## Tests

The tests carried out are enacted in such a way as to simulate common scenarios encountered in typical web applications.
When a request is made for data, the server creates a `Promise`.
The `Promise` becomes resolved once the request in question is done, with the data from the completed request.
This `Promise` API is used for every test, even where it is unnecessary, for scalability and consistency.

The independent variable in these tests is the time between an endpoint receiving an initial request for a page, and the initiation of requests for external data.
This intentionally-altered time is essentially the time that lapses until requests can be made for data.
The dependent variable is the time between an endpoint receiving an initial request for a page and a client receiving all of the data it needs.
This is the time it takes for a client to be completely finished with its interactions with the server.
Each individual scenario may also contain several variants, in which a secondary variable is altered in an attempt to establish trends within the scenario.

The way the dependent variable is measured is similar between each testing method.
When an endpoint receives a request from a client, it immediately saves a reference to `Date.now()`, which "return[s] a `Number` value that is the time value designating the UTC date and time of the occurrence of the call to `now`," where the time value "is measured in milliseconds since 01 January, 1970 UTC" [@ecma, section 15.9.4.4].
This `start` value is passed along with the markup for the pages served in each workflow.
Once each testing method is complete, it calls a function `CS.diff`, which records a new value of `Date.now()`, and measures the difference between the two time values.

The only difference between workflows is the determination of the point at which the process is considered to be complete.
For the server workflow, `CS.diff()` is run as soon as the browser executes the JavaScript code on the served page.
Since the `<script>` tags occur immediately before the closing `</body>` tag, the code is loaded and run in an optimal manner [@code-standards, *Optimize Delivery of CSS and JavaScript*].
The AJAX workflow runs `CS.diff()` once it receives all of the responses it is expecting for any given testing scenario.
The stream workflow does the same as the AJAX one, though naturally it receives its data through a different transport.


### Local file

In the local file scenario, a file is read from the local filesystem of the Cornerstone web server.
The size of the file is altered between tests, ranging from an empty file to a large one.
This is a very common situation in web applications, which often need to read local files.

The files are made during an initialization process using the GNU `truncate` command and stored in `/tmp`, which is a `tmpfs` filesystem residing in memory on the server.
When requested, these files are read from the filesystem using the asynchronous Node.js command `fs.readFile`.
After a file is finished being read into the working memory of the web application server, it resolves the request `Promise` with the contents of the file.

Since the `truncate` command merely allocates the size for a file and does not fill it with contents, all of the created files are "empty".
To get around this, we actually send the raw `Buffer` data to the client, as opposed to the `UTF-8` encoded contents.
For example, an "empty" file of size \SI{4}{\byte} is sent to the client as:

```json
{
	"type": "Buffer",
	"data": [
		0,
		0,
		0,
		0
	]
}
```

This is how `Node.js` represents octet streams, where `data` is an `Array` of \si{bytes} corresponding to the "raw memory allocation outside the V8 heap" [@node, *Buffer*].

See \autoref{fig:dataLocalFile} for the data captured for this test case.

\pgfplotstableread{data/local-file.dat} \dataLocalFile

\begin{figure}[H]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time taken to load data from a local file},
			% axes
			xlabel={Size},
			xticklabels from table={\dataLocalFile}{size},
			x unit=\si{\kilo\byte},
		]

			% server
			\addplot+[
				smooth,
				color=red,
				mark=square
			]
				table[y = server] from \dataLocalFile;
			\addlegendentry{Server}

			% ajax
			\addplot+[
				smooth,
				color=blue,
				mark=triangle
			]
				table[y = ajax] from \dataLocalFile;
			\addlegendentry{AJAX}

			% stream
			\addplot+[
				smooth,
				color=violet,
				mark=diamond
			]
				table[y = stream] from \dataLocalFile;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Local file workflow data.}
	\label{fig:dataLocalFile}
\end{figure}


### Local database

Perhaps the most common scenario being tested is retrieving data from a local database.
For our testing we are using a `mongodb` instance, with spurious data stored within several collections.
Each collection contains a varying number of documents in the form of:

```json
{
	"x": n
}
```

where `n` is an incremental value between `0` and the number of entries inside the collection.

See \autoref{fig:dataLocalDB} for the data captured for this test case.

\pgfplotstableread{data/local-database.dat} \dataLocalDB

\begin{figure}[H]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time taken to load data from a local database},
			% axes
			xlabel={Number of documents},
			xticklabels from table={\dataLocalDB}{documents},
		]

			% server
			\addplot+[
				smooth,
				color=red,
				mark=square
			]
				table[y = server] from \dataLocalDB;
			\addlegendentry{Server}

			% ajax
			\addplot+[
				smooth,
				color=blue,
				mark=triangle
			]
				table[y = ajax] from \dataLocalDB;
			\addlegendentry{AJAX}

			% stream
			\addplot+[
				smooth,
				color=violet,
				mark=diamond
			]
				table[y = stream] from \dataLocalDB;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Local database workflow data.}
	\label{fig:dataLocalDB}
\end{figure}


### Timeout

In the timeout scenario, the server sends responses to the client after a (varying) timeout.
When a client hits the endpoint, the server runs a `setTimeout`, after which the response's promise is resolved.
Unlike the previous scenarios, this is a largely contrived workflow on its own, however it is meant to simulate the often asynchronous nature of web applications.

See \autoref{fig:dataTimeout} for the data captured for this test case.

\pgfplotstableread{data/timeout.dat} \dataTimeout

\begin{figure}[H]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time taken to load data with `setTimeout`},
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
				mark=diamond
			]
				table[y = stream] from \dataTimeout;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Timeout workflow data.}
	\label{fig:dataTimeout}
\end{figure}


### Series

The series test scenario involves having multiple requests that need to occur sequentially.
That is, a request can not begin until the request that precedes it has been successfully resolved.
The requests received are simply resolved after a timeout exactly like those in the [timeout](#timeout) test.
Instead of varying the timeout length, however, the value of the timeout is always \SI{0}{\ms}.
Rather, it is the number of serial requests performed that is changed across different trials.

See \autoref{fig:dataSeries} for the data captured for this test case.

\pgfplotstableread{data/series.dat} \dataSeries

\begin{figure}[H]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time taken to load multiple `setTimeout` requests in series},
			% axes
			xlabel={Number of serial requests},
			xticklabels from table={\dataSeries}{requests},
		]

			% ajax
			\addplot+[
				smooth,
				color=blue,
				mark=triangle
			]
				table[y = ajax] from \dataSeries;
			\addlegendentry{AJAX}

			% stream
			\addplot+[
				smooth,
				color=violet,
				mark=diamond
			]
				table[y = stream] from \dataSeries;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Series workflow data.}
	\label{fig:dataSeries}
\end{figure}


### Parallel

Similar to the [series](#series) test, the parallel test scenario deviates in that the requests are all initiated at the same time.
This is a much more common scenario than the series test in practice, as requests are often autonomous.
The number of requests performed is still altered between different variants as in the series test, however the timeout duration is \SI{2000}{\ms} instead of \SI{0}{\ms}.

See \autoref{fig:dataParallel} for the data captured for this test case.

\pgfplotstableread{data/parallel.dat} \dataParallel

\begin{figure}[H]
	\begin{tikzpicture}
		\begin{axis}[
			defaults,
			title={Time taken to load multiple `setTimeout` requests in parallel},
			% axes
			xlabel={Number of parallel requests},
			xticklabels from table={\dataParallel}{requests},
		]

			% server
			\addplot+[
				smooth,
				color=red,
				mark=square
			]
				table[y = server] from \dataParallel;
			\addlegendentry{Server}

			% ajax
			\addplot+[
				smooth,
				color=blue,
				mark=triangle
			]
				table[y = ajax] from \dataParallel;
			\addlegendentry{AJAX}

			% stream
			\addplot+[
				smooth,
				color=violet,
				mark=diamond
			]
				table[y = stream] from \dataParallel;
			\addlegendentry{Stream}

		\end{axis}
	\end{tikzpicture}

	\caption{Parallel workflow data.}
	\label{fig:dataParallel}
\end{figure}
