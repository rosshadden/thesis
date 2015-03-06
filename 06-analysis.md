# Analysis

The local file and local database tests (see figures \ref{dataLocalFile} and \ref{dataLocalDB}, respectively) yield comparatively poor results for the stream method.
Interestingly enough, the timeout test (see figure \ref{dataTimeout}) is consistently better for the stream method than for the server and AJAX methods.

The success and failure of the stream method seems to be due to two distinct factors:   transmitted data size and request duration.
In the timeout test for example (\ref{dataTimeout}), the data that is being transmitted to the client is minimal.
In fact the transmitted data is merely a simple number (the number of milliseconds the timeout was made for).
The file and database tests are sending increasingly more loads of data to the client, as they are sending the contents of files and collections of varied lengths.
It seems logical to conclude based on these tests that the stream method performs better when less data is transmitted.

The timeout test does better for the stream method in all cases except the first, which is when there is a timeout of \SI{0}{\second}.
I believe this is because at \SI{0}{\second}, there is not enough time for the stream method to benefit from bypassing the gap between loading the page and making the request.
That is, by the time the client loads the page and establishes a socket connection to the server, it is essentially equivalent to the AJAX method for a timeout \SI{0}{\second}, in that in makes a request and receives an immediate response.
This is because although the timeout finishes immediately after the original endpoint was hit, it still requires the additional request of establishing the socket connection in order to receive the data.
In the trials with the higher timeouts there is a period of time where the stream method benefits from making the timeout call as soon as the endpoint is hit.


