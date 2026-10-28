# Add subtitles to a video *(local)*

## 1. Extract audio from the original video

> Install [ffmpeg](https://ffmpeg.org/) first.

```bash
ffmpeg -i input.mp4 -vn -ar 16000 -ac 1 -c:a pcm_s16le audio.wav
```

| Parameter | Basic meaning |
| --- | --- |
| `-i input.mp4` | Select the input video named `input.mp4`. |
| `-vn` | Do not process the image portion; extract only audio. |
| `-ar 16000` | Set the audio sample rate to 16 kHz. |
| `-ac 1` | Convert the audio to mono (1 channel). |
| `-c:a pcm_s16le` | Encode the audio in PCM 16-bit little-endian format. |
| `audio.wav` | Output audio file name. |


## 2. Create original subtitles with *whisper.cpp*

> Install [whisper.cpp](https://github.com/ggml-org/whisper.cpp) first.

Download the local model ([Available models](https://github.com/ggml-org/whisper.cpp/blob/master/models/README.md)):
```bash
sh ./models/download-ggml-model.sh base.en
```

Generate subtitles:
```bash
whisper-cli -m models/ggml-base.en.bin -f audio.wav -osrt -l en -t 4
```

This command reads the audio file and writes an SRT subtitle file.

| Parameter | Simple meaning |
| --- | --- |
| `-m models/ggml-base.en.bin` | Use the English Whisper model. |
| `-f audio.wav` | Read the audio file to transcribe. |
| `-osrt` | Save the result as an SRT subtitle file. |
| `-l en` | Set the language to English. |
| `-t 4` | Use 4 threads to process faster. |

*(Output: `audio.wav.srt`)*


## 3. Translate subtitles
[LibreTranslate](https://github.com/LibreTranslate/LibreTranslate) is a free and open-source translation API. It can run locally and works offline.

> Example setup
```bash
pip install libretranslate
```

See supported languages: [link](https://docs.libretranslate.com/guides/supported_languages/)
```bash
libretranslate --load-only en,vi
```

Then open browser and go to: http://localhost:5000

## 4. Add subtitles to the video *(optional)*

**Softsub** (subtitle is separate and can be turned on/off):
```bash
ffmpeg -i input.mp4 -i sub_vi.srt -c copy -c:s mov_text output.mp4
```

**Hardsub** (subtitle is burned into the video image):
```bash
ffmpeg -i input.mp4 -vf "subtitles=sub_vi.srt" -c:a copy output_burned.mp4
```

You can also use [VLC](https://www.videolan.org/vlc/download-windows.html) to play the video with subtitles.
