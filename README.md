node-lame
=========
### NodeJS native bindings to libmp3lame & libmpg123
[![Build Status](https://travis-ci.com/schneefux/node-lame.svg?branch=master)](https://travis-ci.com/schneefux/node-lame)

For all your async streaming MP3 encoding/decoding needs, there's `node-lame`!
This module hooks into libmp3lame, the library that the `lame` command uses, to
provide `Encoder` and `Decoder` streams to NodeJS.


Installation
------------

`node-lame` comes bundled with its own copy of `libmp3lame` and `libmpg123`, so
there's no need to have them installed on your system.

This fork of node-lame is published on GitHub packages instead of NPM.
Create an `.npmrc` in your project's root directory. Add the following line so that packages with the `@schneefux` scope will be installed from the GitHub packages repository: `@schneefux:registry=https://npm.pkg.github.com`

You might need to log in to GitHub via npm once. See [GitHub's documentation](https://help.github.com/en/packages/using-github-packages-with-your-projects-ecosystem/configuring-npm-for-use-with-github-packages#installing-a-package) for details.

Then, install `node-lame` using `npm`:

``` bash
$ npm install @schneefux/lame
```


Example
-------

Here's an example of using `node-lame` to encode some raw PCM data coming from
`process.stdin` to an MP3 file that gets piped to `process.stdout`:

``` javascript
var lame = require('lame');

// create the Encoder instance
var encoder = new lame.Encoder({
  // input
  channels: 2,        // 2 channels (left and right)
  bitDepth: 16,       // 16-bit samples
  sampleRate: 44100,  // 44,100 Hz sample rate

  // output
  bitRate: 128,
  outSampleRate: 22050,
  mode: lame.STEREO // STEREO (default), JOINTSTEREO, DUALCHANNEL or MONO
});

// raw PCM data from stdin gets piped into the encoder
process.stdin.pipe(encoder);

// the generated MP3 file gets piped to stdout
encoder.pipe(process.stdout);
```

See the `examples` directory for some more example code.

API
---

### Decoder class

The `Decoder` class is a `Stream` subclass that accepts MP3 data written to it,
and outputs raw PCM data. It also emits a `"format"` event when the format of
the MP3 file is determined (usually right at the beginning).

### Encoder class

The `Encoder` class is a `Stream` subclass that accepts raw PCM data written to
it, and outputs a valid MP3 file. You must specify the PCM data format when
creating the encoder instance. Only 16-bit signed samples are currently
supported (rescale before passing to the encoder if necessary)...
