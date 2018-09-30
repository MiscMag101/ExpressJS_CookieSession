
# How to test it

## Download prototype

```console
$ git clone https://github.com/MiscMag101/ExpressJS_CookieSession.git
```

* Install NPM Packages

```console
$ npm install
```

## Create a self-signed certificat

```console
$ mkdir tls
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls/key.pem -out tls/cert.pem
```

For this certificat, a hostname will be required (such as app.example.com).
/!\ This self-signed certificat should be used only for testing purpose.

## Start Application

```console
$ COOKIESECRET=Secret npm start
```

# Test

Open [https://localhost:3000/](https://localhost:3000/) and check cookies
cookie-session

# How I did it

## Create ExpressJS App

Following instructions from [https://github.com/MiscMag101/ExpressJS_Https](https://github.com/MiscMag101/ExpressJS_Https) (or just fork it).
In this prototype, HTTPS is mandatory to issue Secure Cookie.

## Cookie Session

* Install middleware

```console
$ npm install --save cookie-session
```

* Configure middleware

```javascript
// Load middleware
var cookieSession = require('cookie-session')

// Configure middleware
app.use(cookieSession({
  name: 'session',
  secret: process.env.COOKIESECRET,
 
  // Cookie Options
  path: '/',
  httpOnly: true,
  secure: true, 
  signed: true,
  maxAge: 600000, // 10 minutes
  sameSite: 'strict'
}))
```

* Save data to the user session

```javascript
router.get('/', function(req, res, next) {
  
  let SessionData = req.session;
  SessionData.foo = "Hello from session !";
  
  res.render('index', { title: 'Cookie-Session Prototype' });
});
```

* Read data from the user session

```javascript
router.get('/session', function(req, res, next) {
  
  let foo = req.session.foo;
  
  if (typeof req.session.foo !== 'undefined'){
    res.send(`This will print data from session set earlier: ${req.session.foo}`);
  }else{
      res.send("Session doesn't exist.");
  }
  
});
```
