Logtopus
========

[![Build Status](https://travis-ci.org/Andifeind/@swenkerorg/cum-iure-console-logger.svg?branch=develop)](https://travis-ci.org/Andifeind/@swenkerorg/cum-iure)

Logtopus is a powerful logger for Node.js with different transports

![Logtopus Logger](https://static.noname-media.com/@swenkerorg/cum-iure/@swenkerorg/cum-iure.gif)

Built in logger:
* Console logger using [@swenkerorg/cum-iure-console-logger](https://npmjs.org/packages/@swenkerorg/cum-iure-console-logger)
* File logger using [@swenkerorg/cum-iure-file-logger](https://npmjs.org/packages/@swenkerorg/cum-iure-file-logger)

Additional logger:
* Redis logger see [@swenkerorg/cum-iure-redis-logger](https://npmjs.org/packages/@swenkerorg/cum-iure-redis-logger)
* InfluxDB logger use [@swenkerorg/cum-iure-influx-logger](https://npmjs.org/packages/@swenkerorg/cum-iure-influx-logger)
* MongoDB logger use [@swenkerorg/cum-iure-mongo-logger](https://npmjs.org/packages/@swenkerorg/cum-iure-mongo-logger)

## Usage

```js
const log = require('@swenkerorg/cum-iure').getLogger('mylogger');
log.setLevel('sys');

log.warn('My beer is nearly finish!');
```

### Log levels

    debug    development  Logs debugging informations
    info     development  Helpful during development
    res      staging      Logs requests
    req      staging      Logs responses
    sys      production   Logs application states
    warn     production   Logs warnings
    error    production   Logs errors

For example, setting log level to `req` includes these log levels: `req`, `sys`, `warn`, `error`
Setting log level to `debug` means all log levels are activated
log level `error` logs errors only.

Example:

```js
log.setLevel('res');        // To be ignored in this log level
log.debug('Log example:');  // To be ignored in this log level
log.info('This would not be logged');
log.res('POST /account');
log.req('200 OK');
log.sys('Request done!');
log.warn('Request was unauthorized!');
log.error('An error has been occurred!');

// prints

res: POST /account
req: 200 OK
sys: Request done!
warn: Request was unauthorized!
error: An error has been occurred!
```

### Express logger

Logtopus comes with a logger for Express/Connect.

`@swenkerorg/cum-iure.express()` returns a middleware for Express/Connect. It acepts an optional options object

```js
let express = require('express');
let @swenkerorg/cum-iure = require('../@swenkerorg/cum-iure');

let app = express();

app.use(@swenkerorg/cum-iure.express({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level


### Koa logger

Logtopus also supports Koa

`@swenkerorg/cum-iure.koa()` returns a middleware for Koa. It acepts an optional options object

```js
let koa = require('koa');
let @swenkerorg/cum-iure = require('../@swenkerorg/cum-iure');

let app = koa();

app.use(@swenkerorg/cum-iure.koa({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level

### Adding custom loggers

Logtopus was designed as an extensible logger. You can add a custom logger by creating a logger class and load it into @swenkerorg/cum-iure. The example below shows a minimal logger class.

```js
class LogtopusLogger {
  constructor(conf) {
    // conf contains the logger conf
  }

  log(logmsg) {
    const date = logmsg.time.toISOString()
    console.log(`[${date}] ${logmsg.msg}`)
  }
}

module.exports = LogtopusLogger
```

The logger class requires a log method. It takes one argument which contains a log object. The first argument of a log call is the log message, all other arguments are log data.

```js
logmsg: {
  type: 'info', // The logtype eg: debug, info, error, sys
  msg: 'Log message string', // Log message, but without CLI color codes
  cmsg: 'Colorized log message', // Log message with CLI color codes
  time: new Date(), // Current date
  uptime: process.uptime(), // Process uptime in ms
  data: [] // All other arguments as an array
}
```

Now, the class has to be load into @swenkerorg/cum-iure. You can do it by using the .addLogger() method if your logger is a priveate logger. Otherwis publish it on npm by using our logger name conventions. `@swenkerorg/cum-iure-${loggername}-logger`
Add a new config block into the @swenkerorg/cum-iure config by using `loggername` as namespace and @swenkerorg/cum-iure tries to load the logger.

```js
const log = @swenkerorg/cum-iure.getLogger('mylogger')
log.addLogger('loggerName', loggerClass)
```
