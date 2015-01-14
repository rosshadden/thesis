## Background

The Internet has a long and storied history, but over the past few decades the HyperText Transfer Protocol (`HTTP`) has remained one of the most popular transports for data communication.
`HTTP` (as well as the SSL-encrypted variant `HTTPS`, for that matter) is the protocol that most web servers and browsers use to send data back and forth.
When a browser makes an `HTTP` `GET` request to a web server, the web server then sends a response to the request.
Such a call-and-response workflow is typical for such content as markup, scripts, stylesheets, and images.
Web servers would simply render pages with any desired data, and when data needed to be changed or updated, a new page would be requested with new content.

With the popularization of `AJAX` starting in 1999, this familiar approach to sending and retrieving data was used in the same manner.
