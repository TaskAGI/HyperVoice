# TaskAGI API Documentation

Simple guide to using TaskAGI's APIs.

## HyperVoice V4 API

HyperVoice V4 lets you generate realistic speech from text and clone voices.

### Authentication

Include your API key in all requests:

Authorization: Bearer YOUR_API_KEY


Get your API key from [TaskAGI](https://taskagi.net/).

### Text-to-Speech

Convert text to speech with various voice options.

**Endpoint:** `POST /api/hypervoice/v4/text-to-speech`

**Request:**
```json
{
  "text": "Your text to convert to speech",
  "voice_name": "peter",
  "speaking_rate": 15,
  "context_aware": true,
}
```
Setting context-aware to true will automatically adjust emotion, tone and speed of your speech based on sentiment analysis. You can alternatively set these values yourself: 
```{
  "text": "Your text to convert to speech",
  "voice_name": "emma",
  "speaking_rate": 15,
  "emotion": {
    "happy": 0.7,
    "surprise": 0.3
  }
}
```


Result:
```
{
  "success": true,
  "audio_url": "https://storage.taskagi.net/audio/12345.mp3"
}
```

## Voice Cloning

Clone a voice from an audio sample.

Endpoint: POST /api/hypervoice/v4/voice-clone


### Multipart form data with:
text: "Text to speak in the cloned voice"
speaker_audio: [AUDIO FILE]
speaking_rate: 15

Response:

{
  "success": true,
  "audio_url": "https://storage.taskagi.net/audio/67890.mp3"
}

Examples
Check the examples directory for code samples.



## python_example.py

```python
import requests
import json

# TaskAGI HyperVoice V4 API Example

API_KEY = "YOUR_API_KEY"  # Replace with your actual API key
API_URL = "https://taskagi.net/api/hypervoice/v4"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

# Example 1: Text-to-Speech
def text_to_speech(text, voice_name="emma", speaking_rate=15):
    """Generate speech from text"""
    
    data = {
        "text": text,
        "voice_name": voice_name,
        "speaking_rate": speaking_rate,
        "emotion": {
            "happy": 0.7
        }
    }
    
    response = requests.post(
        f"{API_URL}/text-to-speech",
        headers=headers,
        data=json.dumps(data)
    )
    
    if response.status_code == 200:
        result = response.json()
        print(f"Success! Audio available at: {result['audio_url']}")
        return result['audio_url']
    else:
        print(f"Error: {response.status_code}")
        print(response.text)
        return None

# Example 2: Voice Cloning
def clone_voice(text, audio_file_path, speaking_rate=15):
    """Clone a voice from audio sample"""
    
    with open(audio_file_path, 'rb') as audio_file:
        files = {
            'speaker_audio': audio_file
        }
        
        data = {
            'text': text,
            'speaking_rate': speaking_rate
        }
        
        response = requests.post(
            f"{API_URL}/voice-clone",
            headers={"Authorization": f"Bearer {API_KEY}"},
            data=data,
            files=files
        )
    
    if response.status_code == 200:
        result = response.json()
        print(f"Voice cloned successfully! Audio available at: {result['audio_url']}")
        return result['audio_url']
    else:
        print(f"Error: {response.status_code}")
        print(response.text)
        return None

# Usage examples
if __name__ == "__main__":
    # Text-to-speech example
    text_to_speech("Hello world! This is TaskAGI's HyperVoice V4 API in action.")
    
    # Voice cloning example (uncomment to use)
    # clone_voice(
    #     "This is my cloned voice speaking through TaskAGI's HyperVoice API.",
    #     "path/to/your/voice-sample.mp3"
    # )
