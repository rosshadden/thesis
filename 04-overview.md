> NOTE: possibly combine with the following file

## Overview

As discussed previously, the primary motivation for this study was to reduce a rather large latency gap between serving an initial web page and providing it data.
This was achieved by employing the use of web sockets in a creative way.

When the web server receives a request for a route from a client, it immediately sends a response back to the client with as much content as it can without fetching external data.
If a route needs to make requests to get additional data to be used, these requests are initiated before the response has been sent.
Unlike a typical web server, however, Cornerstone does not wait until the fetched data has been received in order to send a response to the client.
Instead, it sends the received data through to the client as the data arrives, via web sockets.
The client is still free to do whatever it needs to with the data, and the data itself can still be anything at all, such as `JSON` or `HTML`.

This process has several inherent advantages over the typical AJAX workflow.
For one thing, there is no gap between the server's initial response and the initiation of follow-up data requests.
Requests for more data are made slightly before the initial response has been sent.
The client never actually initiates requests for more data, and thus it does not have to wait for a web page to finish completely rendering in order to do so.

This process also effectively reduces the number of requests made.
The server sends just as much data back via responses, but the requests themselves don't actually get made by the client, and therefore do not have any latency at all.
Only one request ever really gets made, which is the very first request the client made to the server to load the main web page.
