# WhisperX Node for ComfyUI

This custom node provides a comprehensive interface for the [WhisperX library](https://github.com/m-bain/whisperx) within ComfyUI, allowing for advanced speech-to-text transcription, word-level timestamp alignment, and speaker diarization.

It has been designed with a robust model management system to handle unstable network connections gracefully.

## Features

- **High-Quality Transcription**: Utilizes `faster-whisper` for fast and accurate speech-to-text.
- **Translation**: Supports translating audio from any supported language into English.
- **Word-Level Timestamps**: Generates precise timestamps for each word using forced alignment.
- **Speaker Diarization**: Identifies and labels different speakers in the audio using `pyannote.audio`.
- **Robust Model Management**: Features an intelligent model downloader with **resume** and **auto-retry** capabilities, making it resilient to network interruptions.
- **Rich Output Formats**: Generates multiple subtitle and data formats including SRT, VTT, TXT, TSV, and JSON.
- **Extensive Customization**: Provides access to a wide range of Whisper and WhisperX parameters for fine-tuning the transcription process.
- **Clear UI & Logging**: Provides detailed, colored logging and multiple progress bars to clearly indicate the current status (downloading, loading, transcribing, etc.).

## Installation

1.  Place this directory (`whisperX`) inside your `ComfyUI/custom_nodes/` folder.
2.  Open a terminal or command prompt, navigate to this directory (`ComfyUI/custom_nodes/whisperX/`), and install the required Python packages by running:
    ```bash
    pip install -r requirements.txt
    ```
3.  Restart ComfyUI.

## Inputs / Parameters

The node is split into "Required" and "Optional" parameters for clarity.

### Required

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `audio` | AUDIO | The audio input to be transcribed. |
| `model` | COMBO | The Whisper model to use for transcription (e.g., `large-v3`, `medium.en`). |
| `language` | COMBO | The language of the audio. Set to `None` for automatic detection. |
| `task` | COMBO | `transcribe` for speech-to-text in the original language, or `translate` to translate to English. |
| `batch_size` | INT | Batch size for transcription. Higher values may be faster on powerful GPUs. |
| `compute_type` | COMBO | The precision for computation (e.g., `float16`, `int8`). `float16` is recommended for modern GPUs. |
| `device` | COMBO | The device to run the models on (`cuda` or `cpu`). |

### Optional

This section provides extensive control over the alignment, diarization, and transcription process. For most use cases, the default values are sufficient.

- **Alignment**: Controls word-level timestamp generation.
- **Diarization**: Controls speaker identification.
- **VAD (Voice Activity Detection)**: Parameters for detecting speech segments.
- **Transcription Fine-Tuning**: Advanced parameters to control the Whisper model's generation process (temperature, beam size, etc.).
- **Output Formatting**: Parameters for subtitle generation.

## Outputs

| Name | Type | Description |
| :--- | :--- | :--- |
| `text` | STRING | The full transcribed text as a single string. |
| `segments_json` | STRING | A JSON string representing the transcribed segments with timestamps. |
| `srt` | STRING | The transcription in SubRip (.srt) subtitle format. |
| `vtt` | STRING | The transcription in WebVTT (.vtt) subtitle format. |
| `tsv` | STRING | The transcription in Tab-Separated Values (.tsv) format. |
| `aud` | STRING | A custom format used by some audio applications. |
| `json_result` | STRING | The complete, raw JSON output from WhisperX, containing all details. |

## Advanced Configuration

You can add custom Whisper or Alignment models by creating and editing a `whisperx.json` file in this node's directory. The format should follow:

```json
{
  "whisper_models": {
    "my-custom-whisper-model": "user/my-custom-whisper-repo"
  },
  "custom_align_models": {
    "en": "user/my-custom-align-model-for-english"
  }
}
```

## Troubleshooting

1.  **Download Fails with `NameResolutionError`**: 
    - This is a network/DNS issue on your computer. The most common solution is to change your system's DNS server to a public one like Google (`8.8.8.8`) or Cloudflare (`1.1.1.1`).

2.  **Node runs once successfully, then fails instantly on subsequent runs**:
    - This is likely a VRAM or resource management issue within ComfyUI. The node loads very large models, and sometimes resources are not fully released. 
    - **Workaround**: Restart the ComfyUI server. If the problem persists, try deleting the `__pycache__` directory within this node's folder and then restarting.
