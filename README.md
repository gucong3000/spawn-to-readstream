# spawn-to-readstream

### Description

Make child process spawn behave like a read stream (buffer the error, don't emit end if error emitted).

### Installation

```bash
npm install spawn-to-readstream
```

### Examples

```js
var toReadStream = require('spawn-to-readstream'),
    spawn        = require('child_process').spawn;

toReadStream(spawn('ls', ['-lah'])).on('error', function(err) {
  throw err;
}).on('end', function() {
  console.log('~~~ DONE ~~~');
}).on('data', function(data) {
  console.log('ls data :::', data.toString());
});
```

Limiting the output of the steam

```js
var toReadStream = require('../index'),
    spawn        = require('child_process').spawn,
    limit, buf;

buf   = 0;
limit = 100 * 1024; // 100 Kb

// Note that it's a 'soft' limit, meaning the data you
// receive might be > 100 Kb, because the child process
// was stopped after receiving more than <LIMIT> bytes
toReadStream(spawn('cat', ['/dev/urandom']), limit)
  .on('error', function(err) {
    throw err;
  }).on('end', function(isLimited) {
    console.log('done, ' + buf + ' bytes received');
    if (isLimited) {
      console.log('child process was cut');
    }
  }).on('data', function(data) {
    buf += data.length;
  });
```

### Tests

```bash
npm test
```

## License

MIT
