
# mocha

  Mocha aims to combine the best of several popular JavaScript test frameworks, providing a fun, accessible, robust browser & node.js based test experience.

## About

  Mocha tests run serially, easing debugging and making it an ideal choice when mocking and stubbing is involved. Existing frameworks such as [expresso](http://github.com/visionmedia/expresso) can be much faster, though not without cost, Mocha aims to be the simple and "fun" test framework.

## Features

  - proper exit status for CI support etc
  - ideal for asynchronous APIs
  - auto-detects and disables coloring for non-ttys
  - maps uncaught exceptions to the correct test case
  - async test timeout support
  - growl notification support
  - reports test durations
  - highlights slow tests
  - global variable leak detection
  - configurable test-case timeout
  - optionally run tests that match a regexp
  - auto-exit to prevent "hanging" due to an active event loop
  - easily meta-generate suites & test-cases
  - mocha.opts file support
  - `mocha-debug(1)` for node debugger support
  - detects multiple calls to `done()`
  - TextMate bundle
  - use any assertion library you want ([should](http://github.com/visionmedia/should.js), `assert`, etc)
  - extensible reporting
    - `dot`: a dot matrix
    - `doc`: a documentation generator (html) based on your tests
    - `landing`: a unicode landing strip ✈
    - `tap`: test-anything-protocol (TAP) producer
    - `progress`: a progress bar
    - `spec`: hierarchical specification
    - `list`: similar to `spec` as a flat-list
    - `json-stream`: streaming JSON delimited by a LF
    - `json`: a single JSON chunk on exit
  - extensible test DSLs
    - BDD
    - TDD
    - exports

## Usage

```

Usage: mocha [options] [files]

Options:

  -h, --help             output usage information
  -V, --version          output the version number
  -r, --require <name>   require the given module
  -R, --reporter <name>  specify the reporter to use
  -u, --ui <name>        specify user-interface (bdd|tdd|exports)
  -g, --grep <pattern>   only run tests matching <pattern>
  -t, --timeout <ms>     set test-case timeout in milliseconds [2000]
  -s, --slow <ms>        "slow" test threshold in milliseconds [75]
  -G, --growl            enable growl support

Reporters:

  dot - dot matrix
  doc - html documentation
  json - single json object
  progress - progress bar
  list - spec-style listing
  tap - test-anything-protocol
  landing - unicode landing strip
  json-stream - newline delimited json events

Interfaces:

  bdd - describe() / it()
  tdd - suite() / test()
  exports - module.exports

```

## Interfaces

 Mocha "interfaces" providing BDD, TDD, and expresso export-style flavoured APIs on top of the internals.
 
### BDD

```js
describe('Array', function(){
  before(function(){
    // ...
  });

  describe('#indexOf()', function(){
    it('should return -1 when not present', function(){
      [1,2,3].indexOf(4).should.equal(-1);
    });

    it('should return the index when present', function(){
      [1,2,3].indexOf(3).should.equal(2);
      [1,2,3].indexOf(2).should.equal(1);
      [1,2,3].indexOf(1).should.equal(0);
    });
  });
});
```

### TDD

```js
suite('Array', function(){
  setup(function(){
    // ...
  });

  suite('#indexOf()', function(){
    test('should return -1 when not present', function(){
      assert.equal(-1, [1,2,3].indexOf(4));
    });

    test('should return the index when present', function(){
      assert.equal(2, [1,2,3].indexOf(3));
      assert.equal(1, [1,2,3].indexOf(2));
      assert.equal(0, [1,2,3].indexOf(1));
    });
  });
});
```

### Exports

```js
module.exports = {
  'Array': {
    '#indexOf()': {
      'should return -1 when not present': function(){
        [1,2,3].indexOf(4).should.equal(-1);
      },
      
      'should return the index when present': function(){
        [1,2,3].indexOf(3).should.equal(2);
        [1,2,3].indexOf(2).should.equal(1);
        [1,2,3].indexOf(1).should.equal(0);
      }
    }
  }
};
```

## Reporters

  Mocha reporters adjust to the terminal window,
  and always disable ansi-escape colouring when
  the stdio streams are not associated with a tty.

### Dot Matrix

  The Dot Matrix reporter is simply a series of dots
  that represent test cases, failures highlight in red.

   ![dot matrix reporter](http://f.cl.ly/items/3b3b471Z1p2U3D1P2Y1n/Screenshot.png)

   ![dot matrix failure](http://f.cl.ly/items/1P11330L033r423g1y1n/Screenshot.png)

## TAP

  The TAP reporter emits lines for a [Test-Anything-Protocol](http://en.wikipedia.org/wiki/Test_Anything_Protocol) consumer.

  ![test anything protocol](http://f.cl.ly/items/2O0X3h0d1Q430O1t1T3p/Screenshot.png)

## Landing Strip

  The Landing Strip reporter is a gimmicky test reporter simulating
  a plane landing :) unicode ftw

  ![landing strip plane reporter](http://f.cl.ly/items/0z1k400K1N1Y2G3u2u0i/Screenshot.png)

## List

  The "List" reporter outputs a simple specifications list as
  test cases pass or fail, outputting the failure details at 
  the bottom of the output.

  ![list reporter](http://f.cl.ly/items/0Y0x1B3l3K0n3t3h3l0p/Screenshot.png)
  
  ![failures](http://f.cl.ly/items/2Z0E150v20042G2d1J0i/Screenshot.png)

## JSON

  The JSON reporter outputs a single large JSON object when
  the tests have completed (failures or not).

## JSON Stream

  The JSON Stream reporter outputs newline-delimited JSON "events" as they occur, beginning with a "start" event, followed by test passes or failures, and then the final "end" event.

```json
["start",{"total":12}]
["pass",{"title":"should return -1 when not present","fullTitle":"Array #indexOf() should return -1 when not present","duration":0}]
["pass",{"title":"should return the index when present","fullTitle":"Array #indexOf() should return the index when present","duration":0}]
["fail",{"title":"should return -1 when not present","fullTitle":"Array #indexOf() should return -1 when not present"}]
["end",{"start":"2011-08-29T03:21:02.050Z","suites":13,"passes":11,"tests":12,"failures":1,"end":"2011-08-29T03:21:02.052Z","duration":2}]
````

## Doc

 The "doc" reporter outputs a hierarchical HTML body representation
 of your tests, wrap it with a header, footer, some styling and you
 have some fantastic documentation!

 For example suppose you have the following JavaScript:

```js
describe('Array', function(){
  describe('#indexOf()', function(){
    it('should return -1 when the value is not present', function(){
      [1,2,3].indexOf(5).should.equal(-1);
      [1,2,3].indexOf(0).should.equal(-1);
    })
  })
})
```

 The command `mocha --reporter doc array` would yield:

```html
<section class="suite">
  <h1>Array</h1>
  <dl>
    <section class="suite">
      <h1>#indexOf()</h1>
      <dl>
      <dt>should return -1 when the value is not present</dt>
      <dd><pre><code>[1,2,3].indexOf(5).should.equal(-1);
[1,2,3].indexOf(0).should.equal(-1);</code></pre></dd>
      </dl>
    </section>
  </dl>
</section>
```

## Async tests

 Testing async code with mocha is simple, invoke the `done()` callback
 when complete, if called multiple times (due to a race-condition etc)
 will cause mocha to fail, this is invaluable for testing async code.

```js
describe('something async', function(){
  it('should finish after 300ms', function(done){
    setTimeout(done, 300);
  })
})
```

 The `done()` callback also accepts an error, so it's easy to write
 tests that adhere to node's callback convention of `(err, result)`:

 ```js
 describe('User.save()', function(){
   it('should save without failing', function(done){
     var user = new User('tj');
     user.save(done);
   })
 })
 ```

## Best practices

### test/*

 By default `mocha(1)` will use the pattern `./test/*.js`, so
 it's usually a good place to put your tests.

### mocha.opts

 Mocha will attempt to load `./test/mocha.opts`, these are concatenated with `process.argv`, though command-line args will take precedence. For example suppose you have the following _mocha.opts_ file:

```
--require should
--reporter dot
--ui bdd
```

  This will default the reporter to `dot`, require the `should` library,
  and use `bdd` as the interface. With this you may then invoke `mocha(1)`
  with additional arguments, here enabling growl support and changing
  the reporter to `spec`:

```
$ mocha --reporter list --growl
```

### Suite merging

  Suites with common names are "merged" in order
  to produce unified reporting, especially when
  meta-generating tests.

```js
describe('merge', function(){
  describe('stuff', function(){
    describe('one', function(){
      it('should do something', function(){
        
      })
    })
  })
})

describe('merge', function(){
  describe('stuff', function(){
    describe('two', function(){
      it('should do something', function(){
        
      })
    })
  })
})

describe('merge stuff', function(){
  describe('three', function(){
    it('should do something', function(){
      
    })
  })
})
```

will produce the following:

  ![mocha suite merging](http://f.cl.ly/items/380R3S1t1t0b0O2K250V/Screenshot.png)

### Makefiles

  Be kind and don't make developers hunt around in your docs to figure
  out how to run the tests, add a `make test` target to your _Makefile_:

```
test:
  ./node_modules/.bin/mocha \
    --reporter list

.PHONY: test
```

## TextMate Bundle

 The Mocha TextMate bundle includes snippets to
 make writing tests quicker and more enjoyable.
 To install the bundle run:
 
     $ make tm

## Running tests

 Run mocha tests:

     $ make test

 Run all tests, including interfaces:

     $ make test-all

 Alter the reporter:

     $ make test REPORTER=list

## License 

(The MIT License)

Copyright (c) 2011 TJ Holowaychuk &lt;tj@vision-media.ca&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.