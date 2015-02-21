# Data

At its core, the testing performed is an attempt to find the fastest or best available method for serving web pages to users.
In most of the test scenarios, these pages contain data populated from external requests.
The data can be anything from returning a value after a timeout to downloading and serving the entire text of a novel.
The need to get data to a client is a common one, and these tests were carried out with that in mind.


## Variables

The independent variable is the time between an endpoint receiving an initial request for a page, and requests for external data.
Essentially, the time until requests for data are made is the intentionally altered variable.
The dependent variable is the time between an endpoint receiving an initial request for a page, and a client receiving all of the data it needs.
In short, this is the time it takes for a client to be completely finished with its interactions with the server.


## Cases

All of the testing scenarios carried out are broken up into three different test cases.
These will henceforth be referred to as the "server", "AJAX", and "stream" workflows.


### Server

The first of these cases, the server workflow, is to have the server carry out all external requests.
After receiving a request from a client to load an endpoint that requires additional data, the server makes the necessary requests.
Once all of the relevant data has been fetched, it serves the page to the client the requested it, with all pertinent data included within the markup.
Such a workflow is not always applicable in web applications, especially for those with dynamic content, however this is nonetheless a common and mature procedure for websites.
Thus this is meant to be more of a control case, against which the other two tests are benchmarked.


### AJAX

The AJAX workflow is perhaps currently the most common standard for websites serving content any more elaborate than simple static markup.
When an endpoint receives a request from a client that requires additional data, it serves back minimal markup without the data present.
The client then makes AJAX calls to request the rest of the data.
As the client receives the responses for this additional data, it does what it needs to do with it (which is in this case render it to the page and perform the necessary testing calculations).


### Stream

The stream workflow employs the process detailed earlier in this paper, which is theoretically a middle ground between the two aforementioned workflows, with the advantages of AJAX and the speed of the server workflow.
In terms of the test cases, the server makes requests for any external data as soon as a client hits an endpoint, not unlike the aforementioned server workflow.
The server then immediately responds to the client with minimal markup without the data present, not unlike the above AJAX workflow.
As the server receives the responses for the additional data, it sends it to the client via an established WebSocket connection.


## Tests


### Base


### Timeout

\pgfplotstableread{data/timeout.dat} \dataTimeout

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
