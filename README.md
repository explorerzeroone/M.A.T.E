# M.A.T.E Voice Assistant

## M.A.T.E or Mechanical Assistant for Technical Exploration
A voice assistant for your PC that listens through your microphone, thinks using a local AI model running in [Ollama](https://ollama.com), and talks back in a dry, deadpan voice inspired by TARS from _Interstellar_ and also from the Marvel Cinematic Universe's J.A.R.V.I.S. The project is at a very primitive state and honestly, I've barely made it work. and there is still a lot of work to be done on it. It's not quite chatGPT or Claude by any means but hey, it works. I'll use the same ideas and principles to integrate it into hardware soon.

The speech recognition and the AI model run on your own computer. Only the text-to-speech step uses the internet (see "How it works").

## How it works

1. You press Enter, speak, and press Enter again.
2. **faster-whisper** turns your speech into text (runs offline on your CPU).
3. The text is sent to a local **Ollama** model, which replies in character.
4. **edge-tts** turns the reply into speech (needs internet), and **ffmpeg** adds a slight robotic effect.
5. The reply plays through your speakers. Press any key to interrupt it.

## Requirements

- Windows 10 or 11 (the audio playback and key-interrupt use Windows-only features)
- Python 3.10 or newer
- A working microphone and speakers
- [Ollama](https://ollama.com/download) installed and running
- [ffmpeg](https://ffmpeg.org) installed
- Internet connection (only for the voice generation step)

## Setup

**1. Install Ollama and download a model**

```
ollama pull qwen2.5:3b
```

Any other chat model works too. Change `OLLAMA_MODEL` at the top of `voice_chat.py` to match.

**2. Install ffmpeg**

```
winget install Gyan.FFmpeg
```

Close and reopen your terminal afterwards. Check it worked with `ffmpeg -version`.

**3. Install the Python libraries**

```
python -m pip install -r requirements.txt
```

**4. Run it**

```
python voice_chat.py
```

The first run downloads the speech recognition model (about 150 MB), so it may pause for a minute.

## Customising

All the settings are at the top of `voice_chat.py`:

| Setting        | What it does                                                            |
| -------------- | ----------------------------------------------------------------------- |
| `OLLAMA_MODEL` | Which Ollama model to use                                               |
| `VOICE`        | The edge-tts voice (run `python -m edge_tts --list-voices` to see all)  |
| `RATE`         | Speaking speed, e.g. `-10%` is slower                                   |
| `PITCH`        | Voice pitch, e.g. `-25Hz` is deeper                                     |
| `ROBOT_FILTER` | The ffmpeg audio effect. Remove the `acrusher` part for a cleaner sound |

The personality lives in the system prompt (the `history = [...]` line). Edit it to make the assistant behave however you like. With TARS, you can ask it to change its humor or honesty settings mid-conversation.

## Troubleshooting

- **`pip` launcher error:** use `python -m pip install ...` instead of `pip install ...`.
- **`cublas64_12.dll` not found:** the code already forces CPU mode (`device="cpu"`). Make sure that argument is present in the `WhisperModel(...)` line.
- **The assistant "hears" words you never said:** background noise can trigger the speech recognizer. Speak a little longer, or reduce room noise.
- **No sound:** check that ffmpeg is installed and that internet is working (edge-tts needs it).
- **Permission denied writing an mp3:** the program writes to your Windows `TEMP` folder, so this should not happen. If it does, check your antivirus.

## Ideas for later

- Fully offline voice using Piper or Kokoro instead of edge-tts
- A wake word instead of pressing Enter
- A desk version using an ESP32 as the microphone and speaker, with this program as the brain over Wi-Fi

## Credits

Built with [faster-whisper](https://github.com/SYSTRAN/faster-whisper), [Ollama](https://ollama.com), [edge-tts](https://github.com/rany2/edge-tts) and [ffmpeg](https://ffmpeg.org). The personality is an unofficial fan tribute to TARS from _Interstellar_ and is not affiliated with the film.
