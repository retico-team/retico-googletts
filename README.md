# retico-googletts

This project contains the incremental module for running Google Cloud TTS in a retico
environment. Google TTS provides multiple languages and voices, which can be looked
up [here](https://cloud.google.com/text-to-speech/docs/voices).

The current implementation of the TTS module is not strictly incremental, as a new input
triggers a complete resynthesizing of the speech.

## Installing

In order to properly use the Text-To-Speech module, you must install two other external software.

#### gcloud
This is to connect you to GCP's (Google Cloud Platform) resources and services which you can install [here](https://docs.cloud.google.com/text-to-speech/docs/get-started).

#### ffmpeg

This is for converting the received MP3 audio from Google's Text-To-Speech synthesizer into a PCM format which can be installed [here](https://ffmpeg.org/download.html).


After installing both external software, you can now install the package with

```bash
$ pip install git+https://github.com/retico-team/retico-googletts
```


## GoogleTTS Example

```python
from retico_core import *
from retico_googleasr import *
from retico_googletts import *


def callback(update_msg):
    for x, ut in update_msg:
        print(f"{ut}: {x.text} ({x.stability}) - {x.final}")


mic = audio.MicrophoneModule()
asr = GoogleASRModule(rate=16_000)
td = text.TextDispatcherModule()
tts = GoogleTTSModule(language_code="en-US", voice_name="en-US-Standard-G")
ad = audio.AudioDispatcherModule(target_frame_length=0.2)
ss = audio.StreamingSpeakerModule(frame_length=0.2)
cb = debug.CallbackModule(callback)

mic.subscribe(asr)
asr.subscribe(td)
td.subscribe(tts)
tts.subscribe(ad)
asr.subscribe(cb)
ad.subscribe(ss)

mic.run()
asr.run()
td.run()
tts.run()
ad.run()
ss.run()
cb.run()

print("Running")
input()

mic.stop()
asr.stop()
td.stop()
tts.stop()
ad.stop()
ss.stop()
cb.stop()
```