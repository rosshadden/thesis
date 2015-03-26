# Analysis

## Explanation

Since the request latency itself is not easily controlled as previously mentioned, the only sensible option is to limit the number of requests, which is the core concept behind all of the executed testing scenarios.
The process outlined in the [AJAX](#ajax) section requires $n + 1$ HTTP requests to a web server, where $n$ is the number of necessary AJAX requests, and naturally $n + 1$ HTTP responses.
This means that there are $2n + 2$ total data transmissions.

The process detailed in the [stream](#stream) section improves upon this by limiting the number of transmissions.
Establishing the WebSocket connection is an event that happens only once per page.
After that point, the connection remains open and may be used indefinitely.
The stream workflow therefore only requires $2$ requests to a web server.
One request is made for the initial page endpoint, and one is made to establish a WebSocket connection.
As there are still the $n + 1$ responses present in the AJAX workflow as well as one response for the WebSocket connection, there are a total of $n + 2$ responses, and thus $n + 4$ total data transmissions.

The [server](#server) process effectively limits those numbers down to one request and one response, however there are inherent problems with pursuing such a low extreme as well.
Most notably, users are presented with a functionless blank page until the response is received.


## Results

The [file](#local-file) and [database](#local-database) tests (see \autoref{fig:dataLocalFile} and \autoref{fig:dataLocalDB}, respectively) yield comparatively poor results for the stream method.
Interestingly enough, the [timeout](#timeout) test (see \autoref{fig:dataTimeout}) is consistently better for the [stream workflow](#stream) than for the server and AJAX workflows, however slightly.

Based solely on these three tests, the success and failure of the stream method seems to be dependent on two distinct factors:  transmitted data size and request duration.
In the timeout test for example, the data that is being transmitted to the client is minimal.
In fact the transmitted data is merely a small integer (the number of milliseconds the timeout was made for).
The file and database tests are sending increasing loads of data to the client, as they are sending the contents of files and collections of varied lengths.
It seems logical to conclude based on these tests that the stream method performs better when less data is transmitted.

The timeout test does better for the stream workflow in all cases except the first, which is when there is a timeout of \SI{0}{\ms}.
I believe this is because at \SI{0}{\second}, there is not enough time for the stream method to benefit from bypassing the gap between loading the page and making the request.
That is, by the time the client loads the page and establishes a WebSocket connection to the server, it is essentially equivalent to the AJAX method for a timeout of \SI{0}{\ms} in that in makes a request which is immediately resolved.
This is because although the timeout finishes immediately after the original endpoint was hit, it still requires the additional request of establishing the socket connection in order to receive the data.
In the trials with the higher timeouts, however, there is a period of time where the stream method benefits from making the timeout call as soon as the endpoint is hit.
The data supports this theory because in every case of the timeout test (except the \SI{0}{\ms} case), the stream method is actually around \SI{600}{\ms} faster than the other two methods.
This time of \SI{600}{\ms} happens to be around the time taken to load a basic page from the web server (see \autoref{table:base}).

It is worth noting that in the timeout test every trial for the stream workflow is extraordinarily close between repeated trials.
Repeated trials in every other scenario were found to vary by around \num{100}--\num{200} \si{\ms} between tests without any variables changing, and the values seen in the [data](#data) section are averages of these numbers.
The timeout trials were instead found to vary by only \num{0}--\num{3} \si{\ms} between trials.
The timeout test is the only test found to exhibit this behavior, and only for the stream method.

The [series](#series) and [parallel](#parallel) tests show much more promising results for the stream method.
In the series scenario, the results of the AJAX and stream workflows very quickly diverge.
Whereas the AJAX method yields values on a linearly increasing scale, the stream method instead appears to adhere to more of a logarithmic growth.
Here the benefits of WebSockets really start to become evident, as depicted in \autoref{fig:dataSeries}.
The reason the stream method performs so much better than AJAX when operating with serial requests is that there is only one single WebSocket connection, which remains open throughout the entire endeavor.
In contrast, each one of the requests in the AJAX method makes a separate call to the server, creating a new connection.
In the latter variant, there are 1024 separate AJAX requests made to the server, while there is only ever one WebSocket connection.

The series testing scenario is a logical extension of the theory that this entire paper was created for, which is that the gap between requests may be mitigated or even entirely circumvented by making intelligent uses of WebSockets.
It takes this concept to an extreme, in which there are many such gaps omitted.
There is overhead in making requests, such as the SYN and ACK handshake between client and server, as well as the HTTP headers that need to be sent with each payload.
In bypassing the latency gaps, this overhead is eliminated as well.

The parallel testing scenario is even more intriguing.
There is almost no overhead to the server and stream workflows.
In fact, due to the relatively tremendous load time of the AJAX method skewing the range set of the data, the overhead is not even noticeable in \autoref{fig:dataParallel}.

What is not clear based purely on the data is the reason why the AJAX method performs so poorly.
This is actually because web browsers have a maximum concurrent connection limit per given hostname [@browserscope, *network*].
The tests were all performed in Chromium 42.0.2311.22, which has a limit of \num{6} concurrent connections.
When the browser is asked to make a large number of asynchronous requests it opens as many connections as it can, opening more as previous requests are resolved.
Since the parallel scenario employs a timeout duration of \SI{2}{\second}, each batch of connections lingers for that long before being resolved.
Thus depending on the web browser used, requests are made at a velocity of around 2--8 requests per second.
The server and stream workflows are really only limited by the maximum heap size and memory availability in their runtime environments, which means their velocities are virtually infinite.

These findings for the parallel test were quite unexpected, as the concurrent connection limit was not considered when the implementation was conceived.
The underlying concept is the same as in the series test, however, which is that the stream method does better when there are more request-response cycles for it to bypass.
