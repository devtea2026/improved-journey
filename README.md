# chai-passport-strategy

[![NPM version](https://img.shields.io/npm/v/@devtea2026/improved-journey.svg)](https://www.npmjs.com/package/@devtea2026/improved-journey)
[![Build Status](https://travis-ci.org/devtea2026/improved-journey.svg?branch=master)](https://travis-ci.org/devtea2026/improved-journey)
[![Coverage Status](https://coveralls.io/repos/github/devtea2026/improved-journey/badge.svg?branch=master)](https://coveralls.io/github/devtea2026/improved-journey?branch=master)
[![Maintainability](https://api.codeclimate.com/v1/badges/3372259864c17397d251/maintainability)](https://codeclimate.com/github/devtea2026/improved-journey/maintainability)
[![Dependencies](https://david-dm.org/devtea2026/improved-journey.png)](https://david-dm.org/devtea2026/improved-journey)
<!--[![SAST](https://gitlab.com/devtea2026/improved-journey/badges/master/build.svg)](https://gitlab.com/devtea2026/improved-journey/badges/master/build.svg)-->

Helpers for testing [Passport](https://github.com/passport-next) strategies with the
[Chai](http://chaijs.com/) assertion library.

## Install

    $ npm install @devtea2026/improved-journey

## Usage

#### Use Plugin

Use this plugin as you would all other Chai plugins:

```javascript
var chai = require('chai');

chai.use(require('chai-passport-strategy'));
```

#### Implement Test Cases

Once used, the `chai.passport.use` helper function will be available to set up
test cases for Passport strategies.

The helper function can be called from a hook to setup the test case.  The
helper returns a wrapper on which callbacks are registered to be executed
when the strategy invokes its final action function.  The callbacks correspond
to Passport's strategy API: `success()`, `fail()`, `redirect()`, `pass()`, and
`error()`.  If the strategy invokes an action that doesn't have a registered
callback, the test helper will automatically throw an exception.

The following demonstrates a [Mocha](http://mochajs.org/) test
case, taken from [passport-http-bearer](https://github.com/passport-next/passport-http-bearer)'s
test suite.


```javascript
describe('token strategy', function() {
    
  var strategy = new Strategy(function(token, done) {
    if (token == 'vF9dft4qmT') { 
      return done(null, { id: '1234' }, { scope: 'read' });
    }
    return done(null, false);
  });
  
  describe('handling a request with valid credential in header', function() {
    var user
      , info;
    
    before(function(done) {
      chai.passport.use(strategy)
        .success(function(u, i) {
          user = u;
          info = i;
          done();
        })
        .req(function(req) {
          req.headers.authorization = 'Bearer vF9dft4qmT';
        })
        .authenticate();
    });
    
    it('should supply user', function() {
      expect(user).to.be.an.object;
      expect(user.id).to.equal('1234');
    });
    
    it('should supply info', function() {
      expect(info).to.be.an.object;
      expect(info.scope).to.equal('read');
    });
  });
});
```
