# What happens when someone visits a site for the first time?

* browser sends a GET request
  * no cookie attached
    * no "Cookie" header
* server sees the request, no cookie attached
* server makes a cookie
  * creates a hashed ID value associated with a key
  * "attaches" it to the response to be sent out later
    * writes a "set-cookie" header with the key-value pairs
* creates a new session in the server's session store
  * just an object in memory or a record in a DB
  * keys this new session object by the hashed value attached to the cookie
  * we can access this object later (on subsequent requests) as `req.session`
* some other route handler ultimately sends the response
  * which has the payload (probably HTML)
  * as well as the "set-cookie" header on it
* browser receives the response and stores the cookie as a set of key-value pairs

# What happens the next time the same client visits the site (or just sends any other request to it)?

* the cookie we stored before is attached to the request sent to the server
  * "Cookie" header with key-value pairs
* server looks at the request, sees there's a cookie attached
* server looks to see if that cookie has an ID associated with a session in storage
  * if not, creates a new session and cookie (see above for specifics)
  * if so, attaches a `session` property to the `req` object, which stores this session from before
    * other handlers and middleware can now access `req.session`
    * `req.session` could have a `userId` property on it to maintain persistent login
