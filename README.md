speedtest.net client

## Installation

```bash
$ npm install speedtest-net
```

## Usage

The whole speed test runs automatically, but a lot of events are available
to get more info than you need.

The test comes in two versions. The main one is for use by code, and then
there's another one for use by command line utils, as it displays a
progress bar and optional information output as well.


Code use example:
```javascript
  var speedTest=require('speedtest-net');

  test=speedTest({maxTime:5000});
  
  test.on('data',function(data){
    console.dir(data);
  });
  
  test.on('error',function(err){
    console.error(err);
  });
  
```

Visual use example:
```javascript
  var speedTest=require('speedtest-net');

  speedTest.visual({maxTime:5000},function(err,data){
    console.dir(data);
  });
```

## Options

You can pass an optional `options` object.

The options include:
* `maxTime` The maximum length of a single test run (upload or download)
* `pingCount` The number of close servers to ping to find thew fastest one
* `maxServers` The number of servers to run a download test on. The fastest is used for the upload test and the fastest result is reported at the end.
* `headers` Headers to send to speedtest.net
* `log` (Visual only) Pass a truthy value to allow the run to output results to the console in addition to showing progress, or a function to be used instead of `console.log`.
* `serverId` ID of the server to restrict the tests against.

## Events

There are a number of events available during the test:

### downloadprogress

Fired when data is downloaded.
The progress in percent is passed.

Note that if more than 1 server is used for download testing, this
will run to 100% multiple times.

```javascript
  require('speedtest-net')().on('downloadprogress',function(progress){
    console.log('Download progress:',progress);
  });
```

### uploadprogress

Fired when data is uploaded.
The progress in percent is passed.

```javascript
  require('speedtest-net')().on('uploadprogress',function(progress){
    console.log('Upload progress:',progress);
  });
```

### error

Fired when an error occurs.
The error is passed.

```javascript
  require('speedtest-net')().on('error',function(err){
    console.log('Speed test error:');
    console.error(err);
  });
```

### config

Fired when the configuration has been fetched from the speedtest server.
The config is passed.

```javascript
  require('speedtest-net')().on('config',function(){
    console.log('Configuration info:');
    console.dir(config);
  });
```

### servers

Fired when the list of servers has been fetched from the speedtest server.
The list of server objects is passed.

```javascript
  require('speedtest-net')().on('servers',function(servers){
    console.log('Complete list of all available servers:');
    console.dir(servers);
  });
```

### bestservers

Fired after closest servers are pinged.
An ordered list of server objects is passed, fastest first.

```javascript
  require('speedtest-net')().on('bestservers',function(servers){
    console.log('Closest servers:');
    console.dir(servers);
  });
```

### testserver

Fired before download or upload is started on a server.
The server object is passed.

```javascript
  require('speedtest-net')().on('testserver',function(server){
    console.log('Test server:');
    console.dir(server);
  });
```

### downloadspeed

Fired when a download speed is found.
When testing on multiple servers, it's fired multiple times.
The speed in bytes per second is passed.
The associated server is the server passed in the last `testserver` event.

```javascript
  require('speedtest-net')().on('downloadspeed',function(speed){
    console.log('Download speed:',(speed/1000).toFixed(2),'KB/s');
  });
```

### uploadspeed

Fired when the upload speed is found.
The speed in bytes per second is passed.
The associated server is the server passed in the last `testserver` event, which will be the fastest server from download test(s).

```javascript
  require('speedtest-net')().on('uploadspeed',function(speed){
    console.log('Upload speed:',(speed/1000).toFixed(2),'KB/s');
  });
```

### result

Fired when the data has been uploaded to SpeedTest.net server.
The url of the result is passed, or `undefined` on error.

```javascript
  require('speedtest-net')().on('result',function(url){
    if (!url) {
      console.log('Could not successfully post test results.');
    } else {
      console.log('Test result url:',url);
    }
  });
```


### data

Fired when tests are done with all relevant information.
The data is passed.

```javascript
  require('speedtest-net')().on('data',function(data){
    console.dir(data);
  });
```

The returned data is a nested object with the following properties:

* __`speeds`__:
 * `download`: download bandwidth in bytes per second
 * `upload`: upload bandwidth in bytes per second
  
  
*  __`client`__:
 * `ip`: ip of client
 * `lat`: latitude of client
 * `lon`: longitude of client
 * `isp`: client's isp
 * `isprating`: some kind of rating
 * `rating`: another rating, which is always 0 it seems
 * `ispdlavg`: avg download speed by all users of this isp
 * `ispulavg`: same for upload
  
  
* __`server`__:
 * `host`: test server url
 * `lat`: latitude of server
 * `lon`: longitude of something
 * `location`: name of a location, usually a city, but can be anything
 * `country`: name of the country
 * `cc`: country code
 * `sponsor`: who pays for the test server
 * `distance`: distance from client to server (SI)
 * `distanceMi`: distance from client to server (Imperial)
 * `ping`: how long it took to download a small file from the server, in ms
 * `id`: the id of the server

### done

Fired when the test are done.
An object with too much data is passed.
Note that it is not fired when an error occurs.

```javascript
  require('speedtest-net')().on('done',function(dataOverload){
    console.log('TL;DR:');
    console.dir(dataOverload);
    console.log('The speed test has completed successfully.');
  });
```

## License

[MIT](http://choosealicense.com/licenses/mit/)