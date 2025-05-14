# HyperVoice API Documentation

HyperVoice V4 is TaskAGI's advanced text-to-speech and voice cloning API that enables you to generate natural-sounding speech from text and clone voices with high fidelity.
![alt text](https://github.com/TaskAGI/HyperVoice/blob/main/Screenshot%202025-04-01%20073134.png "HyperVoice by TaskAGI")

## HyperVoice V4 API

HyperVoice V4 lets you generate realistic speech from text and clone voices. It's context aware, meaning our AI will automatically adjust emotion for your text. You can manipulate emotions with pre-defined values as well.

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

```
# HyperVoice V3 Text-to-Speech API

This endpoint allows you to generate speech from text using our V3 voices.

**Important:** Please use our V3 API primarily as a fallback when the V4 API is undergoing maintenance. This V3 endpoint is generally faster than V4, making it more suitable for agentic use cases and real-time applications where lower latency is critical.

**Endpoint:**

```
GET https://taskagi.net/api/hypervoice/text-to-speech
```

**Authentication:**

Requires a valid TaskAGI API token sent as a Bearer token in the `Authorization` header.

```http
Authorization: Bearer YOUR_TaskAGI_API_TOKEN
```

**Query Parameters:**

| Parameter  | Type    | Required | Description                                                                                                                               |
| :--------- | :------ | :------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| `gen_text` | string  | Yes      | The text you want to convert to speech.                                                                                                   |
| `actor`    | string  | Yes      | The voice actor name **prefixed with `v3_`**. |
| `speed`    | numeric | No       | The desired speech speed. A value between 0.1 and 2.0. Defaults to `1.0` if not provided.                                                |

**Example Request (using cURL):**

```bash
curl -X GET "https://taskagi.net/api/hypervoice/text-to-speech?gen_text=Hello%2C%20this%20is%20a%20test.&actor=v3_af_alloy&speed=1.1" \
     -H "Authorization: Bearer YOUR_TaskAGI_API_TOKEN" \
     -H "Accept: application/json"
```
*** Available actors for V3 model ***
- V3_af_alloy
- V3_af_aoede
- V3_af_bella
- V3_af_jessica
- V3_af_kore
- V3_af_nicole
- V3_af_nova
- V3_af_river
- V3_af_sarah
- V3_af_sky
- V3_am_adam
- V3_am_echo

**Success Response (200 OK):**

The API returns a JSON object containing the URL to the generated audio file. The audio file might be enhanced if the enhancement service is enabled and successful.

```json
{
  "message": "TTS generated successfully.",
  "audio_url": "https://taskagi.net/storage/resources/audio/generated_audio_filename.mp3"
}
```

**Error Responses:**

*   **400 Bad Request:** Returned if the `actor` parameter does not start with `v3_`.
    ```json
    {
        "error": "Invalid actor format. Only v3 actors are supported."
    }
    ```
*   **422 Unprocessable Entity:** Returned if validation fails (e.g., missing `gen_text` or `actor`).
    ```json
    {
        "errors": {
            "gen_text": [
                "The gen text field is required."
            ]
        }
    }
    ```

