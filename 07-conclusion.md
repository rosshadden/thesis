# Conclusion and Future Work

## Conclusion

In summary, we may conclude that the viability of the streaming method for retrieving data is directly dependent on the situation.
When making multiple requests in parallel, if the number of requests is greater than the maximum allowed concurrent connections for a given browser, it is much faster to use WebSockets as a data transport, or serve all data within the initial request to the server.
If the number of requests is below this threshold then there is no tangible benefit to either method, however as this threshold varies between browsers, the web application has little control over limit.
When making multiple requests in serial, WebSockets are also a more optimal data transport than AJAX, is the former does not have to observe as many request-response cycles.
The WebSocket approach does not have to wait for a web page to fully render before making ancillary data requests.
This benefit is realized more when making requests that return small amounts of data, such as in the [timeout](#timeout) test.
Conversely, when sending large amounts of data such as in the [file](#local-file) and [database](#local-database) tests, AJAX outperforms the WebSocket approach.


## Future Work

Although we name the titular data transport the "stream" method, it is actually not benefiting from the use of "streams", merely using them as a one-time (though reusable) transport.
Thus the research done in this thesis may be furthered by taking more advantage of the streaming nature of WebSockets in several ways.
The main area of interest is that the data could be streamed to the client as it is read by the web application.
Most of the tests carried out in this thesis would not be affected by this change, however if implemented intelligently the [file](#local-file) test could take heavy advantage of this feature.
Instead of returning the entire contents of a file to a client upon request, the web server could rather easily be made to stream the contents of the file as it is read.
This would of course be irrelevant for files below a certain unascertained size threshold, however for larger files this could be an impressive enhancement.
More tests would need to be created around this idea, to find the situations in which it is successful and those in which there is no payoff.

Another notable area that could be explored is sending data updates to clients after an initial data payload has been consumed.
An important need for dynamic web applications is to constantly display updated data to users.
The common approaches to doing this are to make repeated AJAX requests on an interval, implement "long polling" (which is when a client makes requests on a long interval that the server only resolves when data has changed), or to use the suggested WebSocket implementation.
There are already many published studies on the comparison of these methods, but research could be done to relate it to the entire request-response cycle discussed within this thesis, and integrated with many of the testing scenarios covered within.
