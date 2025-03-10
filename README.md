# This fork is for research purposes and is not intended to be production ready


# Experiment 1: Wav2Vec2 Japanese Transcription Integration

This extension adds the capability to extract and save standalone transcriptions from the "jonatasgrosman/wav2vec2-large-xlsr-53-japanese" model when used with the whisperX_research repository.

## Background

The original [whisperX_research repository](https://github.com/dgoryeo/whisperX_research) enhances WhisperX ASR results using the wav2vec2 Japanese model, but it doesn't provide a way to access or save the independent wav2vec2 model transcriptions. This extension solves that problem.

## Features

- Extract and save standalone transcriptions from the wav2vec2 Japanese model
- Process long-form audio with automatic chunking
- Save transcriptions in multiple formats (TXT, SRT, JSON)
- Compare wav2vec2 and WhisperX transcription results
- Command-line interface for easy usage

## Installation

1. Clone the whisperX_research repository:
   ```
   git clone https://github.com/dgoryeo/whisperX_research
   cd whisperX_research
   ```

2. Copy the extension files to the repository:
   ```
   # Copy the wav2vec2_transcriber.py file
   cp /path/to/wav2vec2_transcriber.py .
   
   # Copy the integration script
   cp /path/to/process_audio.py .
   ```

3. Install additional dependencies if needed:
   ```
   pip install transformers librosa
   ```

## Usage

### Basic Usage

```bash
python process_audio.py path/to/your/japanese_audio.mp3 --output-dir transcriptions
```

This will:
1. Process the audio with both the wav2vec2 model and WhisperX
2. Save transcriptions in TXT, SRT, and JSON formats
3. Compare the results and save a comparison file

### Get Only Wav2Vec2 Transcription

```bash
python process_audio.py path/to/your/japanese_audio.mp3 --wav2vec2-only
```

### Get Only WhisperX Transcription

```bash
python process_audio.py path/to/your/japanese_audio.mp3 --whisperx-only
```

### Specify Output Formats

```bash
python process_audio.py path/to/your/japanese_audio.mp3 --formats txt,json
```

### Using a Specific Device

```bash
python process_audio.py path/to/your/japanese_audio.mp3 --device cpu
```

### Full Usage Options

```
usage: process_audio.py [-h] [--output-dir OUTPUT_DIR] [--formats FORMATS]
                      [--device DEVICE] [--wav2vec2-only] [--whisperx-only]
                      [--no-compare] [--language LANGUAGE]
                      [--model-name MODEL_NAME] [--batch-size BATCH_SIZE]
                      audio_path

Process Japanese audio with Wav2Vec2 and WhisperX

positional arguments:
  audio_path            Path to the audio file

optional arguments:
  -h, --help            show this help message and exit
  --output-dir OUTPUT_DIR
                        Directory to save transcription outputs
  --formats FORMATS     Output formats (comma-separated)
  --device DEVICE       Device to run models on (cuda or cpu)
  --wav2vec2-only       Only run Wav2Vec2 transcription
  --whisperx-only       Only run WhisperX transcription
  --no-compare          Skip comparison of results
  --language LANGUAGE   Language code for WhisperX
  --model-name MODEL_NAME
                        Whisper model name
  --batch-size BATCH_SIZE
                        Batch size for WhisperX
```

## Integration with Existing Code

The integration script is designed to work with the existing whisperX_research repository structure. It imports the transcription function from the original repository and integrates it with the new wav2vec2 transcription capabilities.

### If the Import Path Needs Adjustment

You may need to adjust the import path in `process_audio.py` based on the actual structure of the whisperX_research repository:

```python
# Update this line based on the actual import path
from whisperx_research.transcribe import transcribe_with_whisperx
```

## Output Structure

For an input file `japanese_speech.mp3`, the script will generate:

```
transcriptions/
├── japanese_speech.wav2vec2.txt     # Plain text transcription from wav2vec2
├── japanese_speech.wav2vec2.srt     # SRT subtitles from wav2vec2
├── japanese_speech.wav2vec2.json    # Detailed JSON from wav2vec2
├── japanese_speech.whisperx.txt     # Plain text from WhisperX
├── japanese_speech.whisperx.json    # Detailed JSON from WhisperX
├── japanese_speech.comparison.txt   # Comparison between both transcriptions
└── japanese_speech.summary.json     # Summary of processing
```

## How It Works

1. The `Wav2Vec2Transcriber` class handles processing audio with the wav2vec2 model:
   - Loads the Japanese wav2vec2 model
   - Processes audio in chunks to handle long-form content
   - Extracts text transcriptions with timestamps
   - Saves the results in multiple formats

2. The integration script:
   - Provides a command-line interface
   - Handles both wav2vec2 and WhisperX processing
   - Saves results from both models
   - Optionally compares the transcription results

## Customization

The chunking parameters in the `transcribe_audio` method can be adjusted for different audio lengths:

```python
# For very long audio files, you might want to increase the chunk size
result = transcriber.transcribe_audio(
    audio_path, 
    chunk_size_seconds=60.0,
    overlap_seconds=10.0
)
```

## Programmatic Usage

You can also use the transcriber programmatically in your Python code:

```python
from wav2vec2_transcriber import Wav2Vec2Transcriber

# Initialize the transcriber
transcriber = Wav2Vec2Transcriber()

# Transcribe an audio file
result = transcriber.transcribe_audio("your_audio.mp3")

# Print the transcription
print(result.text)

# Save to files
transcriber.save_transcription(result, "output", "your_audio")
```

## Notes

- The wav2vec2 model works best with clear, well-recorded Japanese audio
- Processing speed depends on your hardware and the length of the audio
- For very long audio files, consider adjusting the chunk size parameters
