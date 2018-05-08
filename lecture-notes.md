## Sessions & cookies

- A cookie is a *text file / key value pair* created by *the server* then stored on *the client* and sent when *the client makes future requests to that server*.
- "Session" information is an *"object" (in express-sesion, but elsewhere it could be any data of any structure)* stored on *the server* and is specific to each *client*. In our standard boilerplate configuration (e.g. boilermaker) the session can be accessed / manipulated via *`req.session`*.
- A session cookie is a type of cookie that holds *session id*. It is typically created by the library *express-session*.

## Passport (not involving OAuth)

- Passport middleware attaches methods to the express request object, such as: *`req.login`, `req.logout`*
- In passport, we have to specify a "serialize user" callback and a "deserialize user" callback. The serialize user callback happens when *a user logs in*. The deserialize user callback happens when *for every request*.
- Specifying callbacks for serialize user and deserialize user is important because passport does not know *how to store / interact with "user" info for our particular backend*.
- If we have configured everything correctly, we can access (but not *change*) the currently logged in user via *`req.user`*.
- Passport can keep track of the currently logged in user (per client) only because it is also interacting with *the session (via passport session middleware)*.

**What is the difference between "client" and "currently logged in user"?**

A "client" is each specific browser (or other http request maker) that is being used to interact with the server.

A "currently logged in user" is generally information about any client that the server could store.

## OAuth (not involving passport)

- OAuth is a *protocol* (like HTTP).
- OAuth enables a software application we might build (the *consumer*) to allow an end user (the *user*) to login via a *third party* (the *provider*).

If you want to configure OAuth for a particular provider, there should be a website for that provider that helps you create new OAuth compatibile applications. You'll get a client id and client secret to use for each different application you write for each OAuth provider you setup with (this id and secret are sort of like a username and password—but for your whole application, not for any particular user).

## Passport AND OAuth

To configure a particular provider...

- Install passport "strategy" plugin for that provider
- Import / require that library
- Create a new "strategy" instance by passing it provider config (e.g. client ID and client secret) and a "verify callback" (more on this later)
- `passport.use` that strategy instance to register it with passport
- Define a route handler for the initial login request, whose request handler is given by calling `passport.authenticate` with the correct provider strategy name
- Define a route handler for the callback request, whose request handler is ALSO given by calling `passport.authenticate` with the correct provider strategy name

The verify callback is what gets executed at the "end" of the OAuth flow, just after the provider has verified both the consumer and the end user, but before the consumer application (our app) sends a response.

In the verify callback we will receive profile information from the provider and should 1) find or create a user in our database, 2) pass that user instance to `done` so that the user is "serialized" (logged in) by passport.

Dummy example code:

```js
const ExampleProviderStrategy = require('passport-_EXAMPLE_');
const providerConfig = {
  clientID: /*???*/,
  clientSecret: /*???*/,
  callbackURL: /*???*/
}
const strategy = new ExampleProviderStrategy(providerConfig, (token, refreshToken, profile, done) => {
  // find or create user based on this profile info then call `done(null, user)`, or call `done(err)` on any error that might occur during this time
})
passport.use(strategy)
router.get('/', passport.authenticate('_EXAMPLE_', {scope: /*???*/}))
router.get('/callback', passport.authenticate('_EXAMPLE_', {
  successRedirect: /*???*/,
  failureRedirect: /*???*/
}))
```
