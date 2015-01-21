## TODO

- detailed walkthrough
- cs uses hbs for templates, with data passed by controller
- when a Promise is present in the data, don't wait for it to resolve
- bind a socket event that waits for the client to connect
- once the client establishes a socket connection, send the data when the promises resolve
- delete the socket connection handler


> Since the request latency itself is not easily controlled, the only sensible option is to limit the number of requests.
> The process outlined above requires `n+1` `HTTP` requests to a web server, where `n` is the number of `AJAX` requests, and naturally `n+1` `HTTP` responses.
> This means that there are `2n+2`


## Details
