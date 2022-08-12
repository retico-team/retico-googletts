# retico-googletts

This project contains the incremental module for running Google Cloud TTS in a retico
environment. Google TTS provides multiple languages and voices, which can be looked
up [here](https://cloud.google.com/text-to-speech/docs/voices).

The current implementation of the TTS module is not strictly incremental, as a new input
triggers a complete resynthesizing of the speech.

## How to install

Although the installation of retico-googletts only requires
[retico-core](https://github.com/retico-team/retico-core), there are a few software
dependencies that have to be installed manually

### GCloud

For the authentication with the Google TTS API, the command line argument `gcloud` has
to be availbale. For information visit the [Google Cloud Documentation](https://cloud.google.com/text-to-speech/docs/create-audio-text-client-libraries#client-libraries-install-python). To validate that
the access work, the following command has to return the access token to authenticate
the use of the API:

```bash
$ gcloud auth print-access-token
```

### ffmpeg

In order to convert the received audio files into the proper format, retico-googletts
requires [ffmpeg](https://ffmpeg.org/download.html).

## Documentation

Sadly, there is no proper documentation for retico-googletts right now, but you can 
start using the GoogleTTSModule like this:

```python
from retico_core import *
from retico_googleasr import *
from retico_googletts import *


def callback(update_msg):
    for x, ut in update_msg:
        print(f"{ut}: {x.text} ({x.stability}) - {x.final}")


m1 = audio.MicrophoneModule(5000)
m2 = GoogleASRModule()
m3 = text.TextDispatcherModule(forward_after_final=False)
m4 = GoogleTTSModule("en-US", "en-US-Wavenet-A")
m5 = audio.SpeakerModule()
m6 = debug.CallbackModule(callback)

m1.subscribe(m2)
m2.subscribe(m3)
m3.subscribe(m4)
m4.subscribe(m5)
m2.subscribe(m6)

m1.setup()
m2.setup()
m3.setup()
m4.setup()
m5.setup()
m6.setup()

m1.run()
m2.run()
m3.run()
m4.run()
m5.run()
m6.run()

input()

m1.stop()
m2.stop()
m3.stop()
m4.stop()
m5.stop()
m6.stop()
```