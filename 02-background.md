# Background

The Internet has a long and storied history, but over the past few decades the HyperText Transfer Protocol (`HTTP`) has remained one of the most popular transports for data communication.
`HTTP` (as well as the SSL-encrypted variant `HTTPS`, for that matter) is the protocol that most web servers and browsers use to send data back and forth to each other.
When a browser makes an `HTTP` `GET` request to a web server, the web server then sends a response to the request.

Web servers originally rendered pages with all desired data present, and a new page would be requested with new content when data needed to be changed or updated.
This works quite nicely when a website is just a collection of static pages.
The pages may contain hyperlinks which, when clicked, take users to another page.
The user's browser client makes another request to the same server, which carries out the same process and responds with the new page content.

Server-side programming languages like PHP Hypertext Preprocessor (often referred to with the recursive acronym `PHP`) can make this process much easier to develop.
Prior to such server-side languages, for example, web developers had to duplicate content on every static page just to have the same header and footer.
With `PHP` they are able to include files such as a header and footer on every page.
A more common approach eventually became to have a layout file which has the outside structure and shared content between, and include each specific page at a certain point within the layout template.
This method has been widely adopted by most modern web frameworks.
Although such techniques have vastly improved the field of web development, they do so within the same confines of the static content era.

Such a call-and-response workflow is still typical today for such content as markup, scripts, stylesheets, and images.
As the need arose to load more dynamic data, however, better methods for loading in additional data were constructed.
With the popularization of `AJAX` starting in 1999, this familiar approach to sending and retrieving data was used in a very similar manner.
After the client loads a page, requests are made to obtain additional data as desired, without requiring a browser to refresh or change pages.
This lets developers tie into a model similar to how the browser loads scripts, stylesheets, and images (via `<script>`, `<link>`, and `<img>` `HTML` tags, respectively), but for any arbitrary data.
The static content era ultimately evolved into one where data did not need to be present upon initial page loads, and could instead be populated asynchronously.
