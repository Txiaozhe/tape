# tape

tap-producing test harness for node and shadow-node


# example

``` js
var test = require('tape');

test('timing test', function (t) {
    t.plan(2);

    t.equal(typeof Date.now, 'function');
    var start = Date.now();

    setTimeout(function () {
        t.equal(Date.now() - start, 100);
    }, 100);
});
```

```
$ iotjs example/timing.js
TAP version 13
# timing test
ok 1 should be equal
not ok 2 should be equal
  ---
    operator: equal
    expected: 100
    actual:   107
  ...

1..2
# tests 2
# pass  1
# fail  1
```

# usage

You always need to `require('tape')` in test files. You can run the tests by
usual shadow-node means (`require('test-file.js')` or `iotjs test-file.js`).
example:

```sh
$ iotjs bin/tape.js tests/**/*.js

# things that go well with tape

tape maintains a fairly minimal core. Additional features are usually added by using another module alongside tape.

# methods

```
var test = require('tape')
```

## test([name], [opts], cb)

Create a new test with an optional `name` string and optional `opts` object.
`cb(t)` fires with the new test object `t` once all preceding tests have
finished. Tests execute serially.

Available `opts` options are:
- opts.skip = true/false. See test.skip.
- opts.timeout = 500. Set a timeout for the test, after which it will fail. See test.timeoutAfter.
- opts.objectPrintDepth = 5. Configure max depth of expected / actual object printing. Environmental variable `NODE_TAPE_OBJECT_PRINT_DEPTH` can set the desired default depth for all tests; locally-set values will take precedence.

If you forget to `t.plan()` out how many assertions you are going to run and you
don't call `t.end()` explicitly, your test will hang.

## test.skip(name, cb)

Generate a new test that will be skipped over.

## test.onFinish(fn)

The onFinish hook will get invoked when ALL tape tests have finished
right before tape is about to print the test summary.

## test.onFailure(fn)

The onFailure hook will get invoked whenever any tape tests has failed.

## t.plan(n)

Declare that `n` assertions should be run. `t.end()` will be called
automatically after the `n`th assertion. If there are any more assertions after
the `n`th, or after `t.end()` is called, they will generate errors.

## t.end(err)

Declare the end of a test explicitly. If `err` is passed in `t.end` will assert
that it is falsey.

## t.fail(msg)

Generate a failing assertion with a message `msg`.

## t.pass(msg)

Generate a passing assertion with a message `msg`.

## t.timeoutAfter(ms)

Automatically timeout the test after X ms.

## t.skip(msg)

Generate an assertion that will be skipped over.

## t.ok(value, msg)

Assert that `value` is truthy with an optional description of the assertion `msg`.

Aliases: `t.true()`, `t.assert()`

## t.notOk(value, msg)

Assert that `value` is falsy with an optional description of the assertion `msg`.

Aliases: `t.false()`, `t.notok()`

## t.error(err, msg)

Assert that `err` is falsy. If `err` is non-falsy, use its `err.message` as the
description message.

Aliases: `t.ifError()`, `t.ifErr()`, `t.iferror()`

## t.equal(actual, expected, msg)

Assert that `actual === expected` with an optional description of the assertion `msg`.

Aliases: `t.equals()`, `t.isEqual()`, `t.is()`, `t.strictEqual()`,
`t.strictEquals()`

## t.notEqual(actual, expected, msg)

Assert that `actual !== expected` with an optional description of the assertion `msg`.

Aliases: `t.notEquals()`, `t.notStrictEqual()`, `t.notStrictEquals()`,
`t.isNotEqual()`, `t.isNot()`, `t.not()`, `t.doesNotEqual()`, `t.isInequal()`

## t.deepEqual(actual, expected, msg)

Assert that `actual` and `expected` have the same structure and nested values using
with strict comparisons (`===`) on leaf nodes and an optional description of the assertion `msg`.

Aliases: `t.deepEquals()`, `t.isEquivalent()`, `t.same()`

## t.notDeepEqual(actual, expected, msg)

Assert that `actual` and `expected` do not have the same structure and nested values using
with strict comparisons (`===`) on leaf nodes and an optional description of the assertion `msg`.

Aliases: `t.notEquivalent()`, `t.notDeeply()`, `t.notSame()`,
`t.isNotDeepEqual()`, `t.isNotDeeply()`, `t.isNotEquivalent()`,
`t.isInequivalent()`

## t.deepLooseEqual(actual, expected, msg)

Assert that `actual` and `expected` have the same structure and nested values using
with loose comparisons (`==`) on leaf nodes and an optional description of the assertion `msg`.

Aliases: `t.looseEqual()`, `t.looseEquals()`

## t.notDeepLooseEqual(actual, expected, msg)

Assert that `actual` and `expected` do not have the same structure and nested values using
with loose comparisons (`==`) on leaf nodes and an optional description of the assertion `msg`.

Aliases: `t.notLooseEqual()`, `t.notLooseEquals()`

## t.throws(fn, expected, msg)

Assert that the function call `fn()` throws an exception. `expected`, if present, must be a `RegExp` or `Function`. The `RegExp` matches the string representation of the exception, as generated by `err.toString()`. The `Function` is the exception thrown (e.g. `Error`). `msg` is an optional description of the assertion.

## t.doesNotThrow(fn, expected, msg)

Assert that the function call `fn()` does not throw an exception. `msg` is an optional description of the assertion.

## t.test(name, [opts], cb)

Create a subtest with a new test handle `st` from `cb(st)` inside the current
test `t`. `cb(st)` will only fire when `t` finishes. Additional tests queued up
after `t` will not be run until all subtests finish.

You may pass the same options that [`test()`](#testname-opts-cb) accepts.

## t.comment(message)

Print a message without breaking the tap output. (Useful when using e.g. `tap-colorize` where output is buffered & `console.log` will print in incorrect order vis-a-vis tap output.)

## var htest = test.createHarness()

Create a new test harness instance, which is a function like `test()`, but with
a new pending stack and test state.

By default the TAP output goes to `console.log()`. You can pipe the output to
someplace else if you `htest.createStream().pipe()` to a destination stream on
the first tick.

## test.only(name, cb)

Like `test(name, cb)` except if you use `.only` this is the only test case
that will run for the entire process, all other test cases using tape will
be ignored

## var stream = test.createStream(opts)

Create a stream of output, bypassing the default output stream that writes
messages to `console.log()`. By default `stream` will be a text stream of TAP
output, but you can get an object stream instead by setting `opts.objectMode` to
`true`.

### tap stream reporter

You can create your own custom test reporter using this `createStream()` api:

``` js
var test = require('tape');
var path = require('path');

test.createStream().pipe(process.stdout);

process.argv.slice(2).forEach(function (file) {
    require(path.resolve(file));
});
```
### object stream reporter

Here's how you can render an object stream instead of TAP:

``` js
var test = require('tape');
var path = require('path');

test.createStream({ objectMode: true }).on('data', function (row) {
    console.log(JSON.stringify(row))
});

process.argv.slice(2).forEach(function (file) {
    require(path.resolve(file));
});
```

# install

# license

MIT
