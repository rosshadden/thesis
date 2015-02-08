# Background

The Internet has a long and storied history, but over the past few decades the HyperText Transfer Protocol (`HTTP`) has remained one of the most popular transports for data communication.
`HTTP` (as well as the SSL-encrypted variant `HTTPS`, for that matter) is the protocol that most web servers and browsers use to send data back and forth to each other.
When a browser makes an `HTTP` `GET` request to a web server, the web server then sends a response to the request.
Web servers would simply render pages with any desired data, and a new page would be requested with new content when data needed to be changed or updated.

Such a call-and-response workflow is still typical today for such content as markup, scripts, stylesheets, and images.
As the need to load more dynamic data arose, however, better methods for loading in additional data were constructed.
With the popularization of `AJAX` starting in 1999, this familiar approach to sending and retrieving data was used in a very similar manner.
After the client loads a page, requests are made to obtain additional data as desired, without requiring a browser to refresh or change pages.
This lets developers tie into a model similar to how the browser loads scripts, stylesheets, and images (via `<script>`, `<link>`, and `<img>` `HTML` tags, respectively), but for any arbitrary data.
