> NOTE: possibly combine with the following file

## TODO

- high-level overview of what I did
	- send a response ASAP, not waiting for data to be obtained
	- send the data as it comes in


## Overview

As discussed previously, the primary motivation for this study was a rather large latency gap between serving an initial web page and providing it data.
The way this was achieved was by employing the use of web sockets.
When the web server receives a request for a route from a client, it immediately sends a response back to the client with as much content as it can without fetching external data.
If a route needs to make requests to get more data to be used, these requests are initiated before the response has been sent, but unlike a typical web server Cornerstone does not wait until data has been fetched in order to send a response.
Instead, Cornerstone sends the received data through to the client via web sockets as it comes in.
The client is still free to do whatever it needs to with the data, and the data itself can be anything at all, such as `JSON` or `HTML`.

