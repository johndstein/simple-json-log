# simple-json-log

A simple [JSON](#json) logger for [Node.js](https://nodejs.org).

Light weight. No external dependencies. Great for application code or
[libraries](#library-usage).

## Quick Start

```js
const log = new(require('simple-json-log'))({ level: 'info' })
log.info('Hello log world!')
```

Output

```json
{"time":"2019-06-15T08:57:41.016Z","info":"Hello log world!"}
```

We make it easy to log a string message and/or a JSON object.

Parameters are a string message, followed by a JSON object, followed
by an array of keys you want to remove from the output, followed by an
array of values you want to remove from the output.

```js
log.info('Hello world!')
log.info('Hello world!', { some: 'JSON', password: 'letmein' })
log.info('Hello world!', { some: 'JSON', password: 'letmein' }, ['password'])
log.info('Hello world!', { some: 'JSON', password: 'letmein' }, [], ['letmein'])
log.info({ some: 'JSON', password: 'letmein' })
// If there's only one key or value to remove a string works too.
log.info({ some: 'JSON', password: 'letmein' }, 'password')
log.info({ some: 'JSON', password: 'letmein' }, null, 'letmein')
```

Output

```json
{"time":"2019-06-17T03:03:34.952Z","info":"Hello world!"}
{"time":"2019-06-17T03:03:34.953Z","info":"Hello world!","some":"JSON","password":"letmein"}
{"time":"2019-06-17T03:03:34.953Z","info":"Hello world!","some":"JSON"}
{"time":"2019-06-17T03:03:34.953Z","info":"Hello world!","some":"JSON"}
{"time":"2019-06-17T03:03:34.953Z","some":"JSON","password":"letmein"}
{"time":"2019-06-17T03:03:34.953Z","some":"JSON"}
{"time":"2019-06-17T03:03:34.953Z","some":"JSON"}
```

## Errors (and other objects with non-innumerable properties)

`JSON.stringify()` does't do too well with `Error`s or other objects whose
properties are not innumerable.

```js
console.log(JSON.stringify(new Error('What?')))
```

Output (not too helpful)

```json
{}
```

We give you much nicer output.

```js
log.error(new Error('What?'))
```

Output

```json
{
   "time": "2019-06-17T05:30:21.931Z",
   "stack": "Error: What?\n    at Object.<anonymous> (/Users/jds/jds/simple-json-log/index.js:123:13)\n    at Module._compile (module.js:653:30)\n    at Object.Module._extensions..js (module.js:664:10)\n    at Module.load (module.js:566:32)\n    at tryModuleLoad (module.js:506:12)\n    at Function.Module._load (module.js:498:3)\n    at Function.Module.runMain (module.js:694:10)\n    at startup (bootstrap_node.js:204:16)\n    at bootstrap_node.js:625:3",
   "message": "What?"
}
```

## Circular Structures

`JSON.stringify()` also doesn't do well with objects that have
circular references.

The following code will give you the dreaded `TypeError: Converting
circular structure to JSON`.

```js
JSON.stringify(log)
```

We handle circular references without throwing an error.

```js
log.info(log)
```

Output

```json
{"time":"2019-06-17T05:46:46.176Z","out":{"connecting":false,"_hadError":false,"_handle":{"writeQueueSize":0},"_parent":null,"_host":null,"_readableState":{"objectMode":false,"highWaterMark":16384,"buffer":{"head":null,"tail":null,"length":0},"length":0,"pipes":null,"pipesCount":0,"flowing":null,"ended":false,"endEmitted":false,"reading":false,"sync":true,"needReadable":false,"emittedReadable":false,"readableListening":false,"resumeScheduled":false,"destroyed":false,"defaultEncoding":"utf8","awaitDrain":0,"readingMore":false,"decoder":null,"encoding":null},"readable":false,"domain":null,"_events":{},"_eventsCount":2,"_writableState":{"objectMode":false,"highWaterMark":16384,"finalCalled":false,"needDrain":false,"ending":false,"ended":false,"finished":false,"destroyed":false,"decodeStrings":false,"defaultEncoding":"utf8","length":0,"writing":false,"corked":0,"sync":true,"bufferProcessing":false,"writecb":null,"writelen":0,"bufferedRequest":null,"lastBufferedRequest":null,"pendingcb":0,"prefinished":false,"errorEmitted":false,"bufferedRequestCount":0,"corkedRequestsFree":{"next":null,"entry":null}},"writable":true,"allowHalfOpen":false,"_bytesDispatched":0,"_sockname":null,"_writev":null,"_pendingData":null,"_pendingEncoding":"","server":null,"_server":null,"columns":167,"rows":52,"_type":"tty","fd":1,"_isStdio":true},"level":"trace","label":"message","levelAsLabel":true,"levelElement":false,"indent":0,"levels":{"trace":0,"debug":1,"info":2,"warn":3,"error":4,"fatal":5,"off":6}}
```

## `util.format()`

There may be times when you want to log an object using `util.format()`.

https://nodejs.org/api/util.html#util_util_format_format_args

```js
// This uses JSON.stringify()
log.info(log)
// This uses util.format()
log.infoUtilFormat(log)
```

Output

```js
{"time":"2019-06-17T01:52:47.063Z","out":{"connecting":false,"_hadError":false,"_handle":{"writeQueueSize":0},"_parent":null,"_host":null,"_readableState":{"objectMode":false,"highWaterMark":16384,"buffer":{"head":null,"tail":null,"length":0},"length":0,"pipes":null,"pipesCount":0,"flowing":null,"ended":false,"endEmitted":false,"reading":false,"sync":true,"needReadable":false,"emittedReadable":false,"readableListening":false,"resumeScheduled":false,"destroyed":false,"defaultEncoding":"utf8","awaitDrain":0,"readingMore":false,"decoder":null,"encoding":null},"readable":false,"domain":null,"_events":{},"_eventsCount":2,"_writableState":{"objectMode":false,"highWaterMark":16384,"finalCalled":false,"needDrain":false,"ending":false,"ended":false,"finished":false,"destroyed":false,"decodeStrings":false,"defaultEncoding":"utf8","length":0,"writing":false,"corked":0,"sync":true,"bufferProcessing":false,"writecb":null,"writelen":0,"bufferedRequest":null,"lastBufferedRequest":null,"pendingcb":0,"prefinished":false,"errorEmitted":false,"bufferedRequestCount":0,"corkedRequestsFree":{"next":null,"entry":null}},"writable":true,"allowHalfOpen":false,"_bytesDispatched":0,"_sockname":null,"_writev":null,"_pendingData":null,"_pendingEncoding":"","server":null,"_server":null,"columns":167,"rows":52,"_type":"tty","fd":1,"_isStdio":true},"level":"trace","label":"message","levelAsLabel":true,"levelElement":false,"indent":0,"levels":{"trace":0,"debug":1,"info":2,"warn":3,"error":4,"fatal":5,"off":6}}
{ time: '2019-06-17T01:52:47.065Z', out:  WriteStream { connecting: false, _hadError: false, _handle:  TTY { writeQueueSize: 0, owner: [Circular], onread: [Function: onread] }, _parent: null, _host: null, _readableState:  ReadableState { objectMode: false, highWaterMark: 16384, buffer: [Object], length: 0, pipes: null, pipesCount: 0, flowing: null, ended: false, endEmitted: false, reading: false, sync: true, needReadable: false, emittedReadable: false, readableListening: false, resumeScheduled: false, destroyed: false, defaultEncoding: 'utf8', awaitDrain: 0, readingMore: false, decoder: null, encoding: null }, readable: false, domain: null, _events: { end: [Object], _socketEnd: [Function: onSocketEnd] }, _eventsCount: 2, _maxListeners: undefined, _writableState:  WritableState { objectMode: false, highWaterMark: 16384, finalCalled: false, needDrain: false, ending: false, ended: false, finished: false, destroyed: false, decodeStrings: false, defaultEncoding: 'utf8', length: 0, writing: false, corked: 0, sync: false, bufferProcessing: false, onwrite: [Function: bound onwrite], writecb: null, writelen: 0, bufferedRequest: null, lastBufferedRequest: null, pendingcb: 1, prefinished: false, errorEmitted: false, bufferedRequestCount: 0, corkedRequestsFree: [Object] }, writable: true, allowHalfOpen: false, _bytesDispatched: 1502, _sockname: null, _writev: null, _pendingData: null, _pendingEncoding: '', server: null, _server: null, columns: 167, rows: 52, _type: 'tty', fd: 1, _isStdio: true, destroySoon: [Function: destroy], _destroy: [Function], [Symbol(asyncId)]: 2, [Symbol(bytesRead)]: 0 }, level: 'trace', label: 'message', levelAsLabel: true, levelElement: false, indent: 0, levels: { trace: 0, debug: 1, info: 2, warn: 3, error: 4, fatal: 5, off: 6 }, timeFn: [Function], replacerFn: [Function], trace: [Function], traceUtilFormat: [Function], debug: [Function], debugUtilFormat: [Function], info: [Function], infoUtilFormat: [Function], warn: [Function], warnUtilFormat: [Function], error: [Function], errorUtilFormat: [Function], fatal: [Function], fatalUtilFormat: [Function], off: [Function], offUtilFormat: [Function] }
```

## Options

**`out`**

Defaults to `process.stdout`. You can use any output stream you
like.

**`level`**

Defaults to 'off'. We do nothing unless you specify a level.

**`levelAsLabel` and `label`**

`levelAsLabel` defaults to `true`.
`label` defaults to 'message'.

If `levelAsLabel` is `true`, we use `level` as the message label.
Otherwise we use `label` as the message label.

**`levelElement`**

Defaults to `false`. If `true` we add a 'level' element to the JSON
output.

**`indent`**

Defaults to `0`. This is the number of spaces to indent the JSON
output.

**`levels`**

Defaults to the following levels. You can specify any levels you like.

```js
{
  trace: 0,
  debug: 1,
  info: 2,
  warn: 3,
  error: 4,
  fatal: 5,
  off: 6
}
```

**`timeFn`**

Defaults to a function that outputs `new Date().toISOString()`. If
`false`, we don't add a 'time' element to the JSON output. Use your
own function to format time however you like.

## Library Usage

To use with a library you could set the `level` based on an environment
variable.

```js
const level = process.env.AWESOME_LIBRARY_LOG_LEVEL || 'off'
```

Or you could use some kind of logic in your library code to load all
the options from a file.

```js
const findUp = require('find-up')
const path = require('path')
const optsPath = findUp.sync('.awesome.library.log.options.js')
const opts = require(path.relative(__dirname, optsPath))
const log = new Logger(opts)
```

There are probably a thousand other ways your library could use
`simple-json-log`. But the basic idea is to make sure it does nothing
by default.

## JSON

JSON is really [JSON](https://www.json.org/)

I use the term 'JSON' loosely here to mean simple objects that are
displayed nicely by `JSON.stringify()`.

