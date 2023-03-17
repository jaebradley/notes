# [How Does A Web Session Work?](https://machinesaredigging.com/2013/10/29/how-does-a-web-session-work/)

* A session is a data structure that applications use to store temporary data that is useful when a user is interacting with the application
* Memory allocated to each user using the application, which is then destroyed when the user quits
* Sessions are stored in a map where the key is the session id and the values are a mapping of data for that session (like the session's user id)
* Browser's generally store the session id as a cookie
* Sessions solve the problem of having to login before making every single HTTP request
  * When the web server receives HTTP request with session id, it looks up, say, the user id and then executes the request given the context of that user id
* After you logout or browser window is closed, session is expired
* One connection = one session id, which means that connecting from two different browsers, you should have two different session ids

