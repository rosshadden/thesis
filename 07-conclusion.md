# Conclusion and other work

## Conclusion

In summary, we may conclude that the viability of the streaming method for retrieving data is directly dependent on the situation.
When making multiple requests in parallel, if the number of requests is greater than the maximum allowed concurrent connections for a given browser, it is much faster to use WebSockets as a data transport, or serve all data within the initial request to the server.
If the number of requests is below this threshold, then there is no tangible benefit to either method.
As this threshold varies between browsers, however, the web application has little or no control over the limit.
When making multiple requests in serial, WebSockets are also a more optimal data transport than AJAX, as the former does not have to observe as many request-response cycles.
The WebSocket approach does not have to wait for a web page to fully render before making ancillary data requests.
This benefit is realized more when making requests that return small amounts of data, such as in the [timeout](#timeout) test.
Conversely, when sending large amounts of data such as in the [file](#local-file) and [database](#local-database) tests, AJAX outperforms the WebSocket approach.

If one of the data transport methods was found to be better in every case, the conclusion drawn might be an advocation of its use over the other transport.
Based on the assorted results, however, this is not the case.
With the knowledge that each method has situations in which they are more effective, the next logical step is to take advantage of both approaches.
Web application servers can decide which transport to use based on the size of the response for example.
If under a certain threshold or if there are multiple requests, the server could send the response via WebSockets.
Otherwise, instead of transmitting the data itself when a client comes online, the server could simply send a message instructing the client that it should hit a specified endpoint with AJAX to receive the necessary data.
The data requests would begin at the same time in this scenario, which would give AJAX the same benefits as observed by the demonstrated WebSocket workflow.
Data would then be sent in the manner that is deemed most efficient based on decided factors.


## Related Work

There are several notable projects similar in nature to this thesis.
One such project is MeteorJS, which resides at the application layer.
It utilizes WebSockets to send data bidirectionally between server and client.
MeteorJS implements a REST-like API on top of WebSockets, and uses this as a data transport in much the same way as AJAX [@meteor, *ddp*].
In fact, MeteorJS has a lot of overlap with Cornerstone, in that they are both web application frameworks built on Node.js and utilize WebSockets.
One of the main differences is that as a full-stack framework, MeteorJS has a large presence on both the server and the client, bridging the gap between the two in such a way that Cornerstone does not set out to achieve.
Thus MeteorJS is in a good position to take full advantage of the benefits of WebSockets as a data transport.

HTTP pipelining is another project aimed at increasing the speed of data transmissions for web sites.
This is an HTTP/1.1 feature in which multiple HTTP requests may be sent on a single TCP connection without waiting for the corresponding responses [@pipelining, section 6.3.2].
It must be supported by both the web server and the clients connecting to the web server in order to work.
Unfortunately, despite the dramatic improvements in data transmission rates when using this technique, it is actually not widely supported by clients.
Although Node.js, Nginx, and other web servers support HTTP pipelining, Opera is the only major browser to have this feature enabled by default [@pipelining-support].
The rest of the major browsers either require users to explicitly enable it or do not support the feature at all.

Google SPDY is a project that began as an improvement over HTTP pipelining, which achieves reduced latency through compression, multiplexing, and prioritization of data [@spdy].
Unlike HTTP pipelining, support of SPDY is very high across major browsers.
SPDY was officially discontinued in February 2015 in favor of HTTP/2, which is based on SPDY itself and will achieve the same performance and latency improvements [@http2].
HTTP/2 will significantly increase the speed of network requests, albeit in a different way than the project detailed by this thesis.
At the time of this writing it is unclear whether WebSockets will still be a viable data transport compared to HTTP/2; the speed of WebSockets may even increase from the changes introduced in HTTP/2.


## Future Work

Although we name the titular data transport the "stream" method, it is actually not benefiting from the use of "streams", merely using them as a one-time (though reusable) transport.
Thus the research done in this thesis may be furthered by taking more advantage of the streaming nature of WebSockets in several ways.
The main area of interest is that the data could be streamed to the client as it is read by the web application.
Most of the tests carried out in this thesis would not be affected by this change, however if implemented intelligently the [file](#local-file) test could take heavy advantage of this feature.
Instead of returning the entire contents of a file to a client upon request, the web server could rather easily be made to stream the contents of the file as it is read.
This would of course be irrelevant for files below a certain unascertained size threshold, however for larger files this could be an impressive enhancement.
More tests would need to be created around this idea to find the situations in which it is successful and those in which there is no measured payoff.

Another notable area that could be explored is sending data updates to clients after an initial data payload has been consumed.
An important need for dynamic web applications is to constantly display updated data to users.
The common approaches to doing this are to make repeated AJAX requests on an interval, implement "long polling" (which is when a client makes requests on a long interval that the server only resolves when data has changed), or to use the suggested WebSocket implementation.
There are already many published studies on the comparison of these methods, but research could be done to relate it to the entire request-response cycle discussed within this thesis, and integrated with many of the testing scenarios covered within.
