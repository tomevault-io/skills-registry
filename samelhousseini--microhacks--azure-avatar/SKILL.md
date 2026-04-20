---
name: azure-avatar
description: Build real-time AI avatars using Azure Voice Live API and GPT-Realtime with WebRTC/WebSocket streaming. Supports two modes for interactive avatar applications. Use when this capability is needed.
metadata:
  author: samelhousseini
---

# Azure Voice Live Avatar Skill

Build real-time interactive AI avatars using Azure Voice Live API and GPT-Realtime.

## Quick Start

```bash
# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.sample .env
# Edit .env with your Azure credentials

# Run the demo
cd scripts
python voice_live_demo.py
# Open http://localhost:5000
```

## Two Connection Modes

### 1. Voice Live API (WebSocket Mode - Recommended)
- Built-in avatar rendering via WebRTC within WebSocket
- Azure semantic VAD for turn detection
- Audio noise reduction and echo cancellation
- Azure TTS voice mapping

### 2. WebRTC Mode (Direct GPT-Realtime)
- Direct browser-to-GPT-Realtime WebRTC connection
- Ephemeral token + SDP negotiation flow
- Native OpenAI voices
- Lower latency

## Architecture

```
Mode 1: Voice Live API (WebSocket)
Browser  <--WebSocket-->  Voice Live API  <-->  GPT-Realtime
                |                 |
                |                 v
                +-- Avatar WebRTC (embedded in session)

Mode 2: WebRTC (Direct)
Browser  <--WebRTC-->  GPT-Realtime API
    |
    +-- Ephemeral Token (via backend)
    +-- SDP Exchange (via backend)
```

## File Structure

```
.github/skills/azure-avatar/
├── SKILL.md              # This documentation
├── .env.sample           # Environment template
├── requirements.txt      # Python dependencies
└── scripts/
    ├── README.md                    # Scripts documentation
    ├── get_ice_token.py             # ICE token retrieval
    ├── get_ephemeral_token.py       # Ephemeral token for WebRTC
    ├── negotiate_sdp.py             # SDP negotiation helper
    ├── voice_live_websocket.py      # Voice Live WebSocket client
    ├── voice_live_demo.py           # Complete Flask demo server
    ├── webrtc_avatar_test.html      # Browser WebRTC test page
    └── static/
        ├── js/
        │   ├── voice-live.js        # Complete client (both modes)
        │   └── audio-processor.js   # AudioWorklet for mic capture
        └── css/
            └── styles.css           # UI styling
```

## Environment Variables

### Voice Live API (WebSocket Mode)
```bash
AZURE_VOICELIVE_ENDPOINT=https://your-resource.cognitiveservices.azure.com/
AZURE_VOICELIVE_API_KEY=your_voicelive_api_key
AZURE_VOICELIVE_MODEL=gpt-realtime
AZURE_VOICELIVE_REGION=swedencentral
AZURE_VOICELIVE_API_VERSION=2025-05-01-preview
```

### GPT-Realtime API (WebRTC Mode)
```bash
AZURE_GPT_REALTIME_SESSION_URL=https://your-openai.openai.azure.com/openai/realtimeapi/sessions?api-version=2025-04-01-preview
AZURE_GPT_REALTIME_KEY=your_gpt_realtime_key
AZURE_GPT_REALTIME_DEPLOYMENT=gpt-realtime
WEBRTC_URL=https://swedencentral.realtimeapi-preview.ai.azure.com/v1/realtimertc
```

### Speech Service (ICE Tokens)
```bash
SPEECH_REGION=swedencentral
AZURE_AI_SPEECH_KEY=your_speech_key
```

## Key Endpoints

| Endpoint | URL Pattern | Purpose |
|----------|-------------|---------|
| Voice Live WebSocket | `wss://{host}/voice-live/realtime?api-key={key}&model={model}` | Main Voice Live connection |
| GPT-Realtime Session | `{openai_endpoint}/openai/realtimeapi/sessions?api-version=...` | Get ephemeral token |
| WebRTC SDP Exchange | `https://{region}.realtimeapi-preview.ai.azure.com/v1/realtimertc` | WebRTC negotiation |
| ICE Token | `https://{region}.tts.speech.microsoft.com/cognitiveservices/avatar/relay/token/v1` | TURN server credentials |
| Speech Token | `https://{region}.api.cognitive.microsoft.com/sts/v1.0/issueToken` | Speech service auth |

## Voice Mapping (OpenAI to Azure TTS)

| OpenAI Voice | Azure TTS Voice |
|--------------|-----------------|
| alloy | en-US-AvaNeural |
| ash | en-US-AndrewNeural |
| ballad | en-US-AriaNeural |
| coral | en-US-JennyNeural |
| echo | en-US-GuyNeural |
| sage | en-US-SaraNeural |
| shimmer | en-US-MichelleNeural |
| verse | en-US-Ava:DragonHDLatestNeural |
| marin | en-US-Andrew:DragonHDLatestNeural |
| cedar | en-US-Brian:DragonHDLatestNeural |

## Avatar Options

### Characters
- `lisa` - Female professional
- `harry` - Male professional
- `jeff` - Male casual
- `lori` - Female casual
- `max` - Male modern
- `meg` - Female modern

### Styles
- `casual-sitting` - Relaxed sitting pose
- `graceful-sitting` - Elegant sitting pose
- `graceful-standing` - Elegant standing pose
- `technical-sitting` - Professional sitting
- `technical-standing` - Professional standing

## Voice Live Session Configuration

Full session configuration with all Azure features:

```javascript
const sessionConfig = {
    type: 'session.update',
    session: {
        modalities: ['text', 'audio'],
        instructions: 'You are a helpful assistant...',
        voice: 'alloy',
        input_audio_format: 'pcm16',
        output_audio_format: 'pcm16',

        // Azure fast transcription
        input_audio_transcription: {
            model: 'azure-fast-transcription'
        },

        // Deep noise suppression
        input_audio_noise_reduction: {
            type: 'azure_deep_noise_suppression'
        },

        // Server echo cancellation
        input_audio_echo_cancellation: {
            type: 'server_echo_cancellation'
        },

        // Semantic VAD turn detection
        turn_detection: {
            type: 'azure_semantic_vad',
            threshold: 0.5,
            prefix_padding_ms: 400,
            silence_duration_ms: 400,
            create_response: true,
            interrupt_response: true,
            remove_filler_words: true
        },

        // Avatar configuration
        avatar: {
            character: 'lisa',
            style: 'casual-sitting',
            customized: false,
            video: {
                bitrate: 2000000,
                codec: 'h264',
                background: { color: '#1a1a2e' }
            }
        }
    }
};
```

## Avatar WebRTC within Voice Live

Voice Live mode embeds avatar via WebRTC:

```javascript
// 1. After session.updated, set up avatar WebRTC
const pc = new RTCPeerConnection({ iceServers, iceTransportPolicy: 'relay' });

pc.addTransceiver('video', { direction: 'recvonly' });
pc.addTransceiver('audio', { direction: 'recvonly' });

const offer = await pc.createOffer();
await pc.setLocalDescription(offer);

// 2. IMPORTANT: Azure expects base64-encoded JSON of RTCSessionDescription
const clientSdpBase64 = btoa(JSON.stringify(pc.localDescription));

websocket.send(JSON.stringify({
    type: 'session.avatar.connect',
    client_sdp: clientSdpBase64
}));

// 3. Handle server SDP in 'session.avatar.connecting' event
function handleAvatarConnecting(msg) {
    const serverSdp = msg.server_sdp;
    // Decode if base64, or use raw
    const answerDesc = serverSdp.startsWith('v=')
        ? { type: 'answer', sdp: serverSdp }
        : JSON.parse(atob(serverSdp));
    await pc.setRemoteDescription(answerDesc);
}
```

## Audio Processing

PCM16 conversion for microphone capture:

```javascript
// Float32 to Int16 PCM
function floatTo16BitPCM(float32Array) {
    const buffer = new Int16Array(float32Array.length);
    for (let i = 0; i < float32Array.length; i++) {
        const s = Math.max(-1, Math.min(1, float32Array[i]));
        buffer[i] = s < 0 ? s * 0x8000 : s * 0x7FFF;
    }
    return buffer;
}

// Send audio to Voice Live
const pcm16 = floatTo16BitPCM(inputData);
const base64Audio = btoa(String.fromCharCode(...new Uint8Array(pcm16.buffer)));
websocket.send(JSON.stringify({
    type: 'input_audio_buffer.append',
    audio: base64Audio
}));
```

## Message Types

### Input Events
- `session.update` - Configure session
- `session.avatar.connect` - Start avatar WebRTC
- `input_audio_buffer.append` - Send audio
- `conversation.item.create` - Send text message
- `response.create` - Request response

### Output Events
- `session.created` - Session established
- `session.updated` - Config applied
- `session.avatar.connecting` - Avatar SDP ready
- `session.avatar.connected` - Avatar streaming
- `session.avatar.error` - Avatar failed
- `input_audio_buffer.speech_started` - VAD detected speech
- `input_audio_buffer.speech_stopped` - VAD silence
- `conversation.item.input_audio_transcription.completed` - User transcript
- `response.audio_transcript.delta` - Streaming text
- `response.audio.delta` - Streaming audio
- `response.done` - Response complete
- `error` - Error occurred

## Token Refresh Strategy

| Token | Refresh Interval | Endpoint |
|-------|------------------|----------|
| ICE Token | Every 24 hours | `avatar/relay/token/v1` |
| Speech Token | Every 9 minutes | `sts/v1.0/issueToken` |
| Ephemeral Key | Per session | `realtimeapi/sessions` |

## Troubleshooting

| Issue | Solution |
|-------|----------|
| ICE token 401 | Check AZURE_AI_SPEECH_KEY is valid |
| Ephemeral token fails | Verify AZURE_GPT_REALTIME_SESSION_URL and key |
| WebRTC SDP fails | Check WEBRTC_URL and ephemeral key format |
| Voice Live 403 | Verify AZURE_VOICELIVE_API_KEY and endpoint |
| Avatar not loading | Ensure SDP is base64-encoded JSON |
| No video/audio | Check browser WebRTC support (Chrome/Edge) |
| Connection drops | Implement reconnection with backoff |

## Cost Optimization

- Use Voice Live API for simpler billing (bundled)
- Call `pc.close()` when done to stop WebRTC
- Implement proper session cleanup
- Keep responses short for faster processing
- Set `remove_filler_words: true` for cleaner output

## Lessons Learned (React Implementation)

### Critical: Microphone Audio Resampling

**Problem**: Browsers do NOT support `sampleRate: 24000` as a getUserMedia constraint. Native sample rates are typically 48000Hz or 44100Hz.

**Solution**: Capture at native sample rate and resample to 24000Hz before sending to Voice Live API.

```javascript
// WRONG - browsers ignore this constraint
const stream = await navigator.mediaDevices.getUserMedia({
  audio: { sampleRate: 24000 }  // IGNORED!
});

// CORRECT - capture at native rate, resample manually
const stream = await navigator.mediaDevices.getUserMedia({
  audio: {
    channelCount: 1,
    echoCancellation: true,
    noiseSuppression: true,
    autoGainControl: true
  }
});

const audioContext = new AudioContext();  // Uses native rate (e.g., 48000)
const nativeSampleRate = audioContext.sampleRate;

// Resample in the audio processing callback
function resampleAudio(inputData, fromSampleRate, toSampleRate) {
  if (fromSampleRate === toSampleRate) return inputData;

  const ratio = fromSampleRate / toSampleRate;
  const newLength = Math.round(inputData.length / ratio);
  const result = new Float32Array(newLength);

  for (let i = 0; i < newLength; i++) {
    const srcIndex = i * ratio;
    const srcIndexFloor = Math.floor(srcIndex);
    const srcIndexCeil = Math.min(srcIndexFloor + 1, inputData.length - 1);
    const fraction = srcIndex - srcIndexFloor;
    // Linear interpolation
    result[i] = inputData[srcIndexFloor] * (1 - fraction) + inputData[srcIndexCeil] * fraction;
  }
  return result;
}
```

### Prevent Audio Feedback

**Problem**: Connecting ScriptProcessor to `audioContext.destination` plays captured audio through speakers, causing feedback.

**Solution**: Use a silent gain node to keep the processor running without playback.

```javascript
// WRONG - causes feedback loop
processor.connect(audioContext.destination);

// CORRECT - silent destination prevents feedback
const silentGain = audioContext.createGain();
silentGain.gain.value = 0;
processor.connect(silentGain);
silentGain.connect(audioContext.destination);
```

### ICE Connection State for UI Updates

**Problem**: `session.avatar.connected` event may not always fire reliably.

**Solution**: Also listen to ICE connection state to determine when connection is established.

```javascript
pc.oniceconnectionstatechange = () => {
  if (pc.iceConnectionState === 'connected') {
    setIsConnected(true);  // UI can now enable controls
  }
};
```

### Backend Proxy for API Keys

**Critical**: Never expose Azure API keys in frontend code. Use a backend proxy:

```javascript
// Backend (Express)
app.get('/api/voicelive-config', (req, res) => {
  const wsEndpoint = VOICELIVE_ENDPOINT.replace('https://', 'wss://');
  const wsUrl = `${wsEndpoint}voice-live/realtime?api-key=${VOICELIVE_API_KEY}&model=${MODEL}`;
  res.json({ wsUrl });
});

// Frontend
const config = await fetch('/api/voicelive-config').then(r => r.json());
const ws = new WebSocket(config.wsUrl);
```

### Session Timeout

Voice Live sessions have a maximum duration of **30 minutes**. Handle gracefully:

```javascript
ws.onmessage = (event) => {
  const msg = JSON.parse(event.data);
  if (msg.type === 'error' && msg.error?.message?.includes('maximum duration')) {
    // Session timed out - offer to reconnect
    showReconnectPrompt();
  }
};
```

## Working React App Structure

A minimal working React implementation requires:

```
avatar-app/
├── server.js                 # Express backend (API proxy)
├── package.json
└── src/
    ├── App.jsx               # Main app with start screen
    ├── App.css
    ├── components/
    │   ├── AvatarChat.jsx    # Avatar UI component
    │   └── AvatarChat.css
    └── hooks/
        └── useVoiceLive.js   # WebSocket + WebRTC hook
```

### Key Environment Variables Used

```bash
AZURE_VOICELIVE_ENDPOINT=https://your-resource.cognitiveservices.azure.com/
AZURE_VOICELIVE_API_KEY=your_key
AZURE_VOICELIVE_MODEL=gpt-realtime
AZURE_VOICELIVE_REGION=swedencentral
AZURE_VOICELIVE_API_VERSION=2025-05-01-preview
AZURE_AI_SPEECH_KEY=your_speech_key  # For ICE tokens
```


# Azure Voice Live Avatar - Lessons Learned

This document captures the key issues encountered and fixes applied while building the Azure Voice Live Avatar React application.

## Issue 1: WebSocket Connection 404 Error

### Symptom
```
WebSocket connection to 'wss://voiceliveaiservices.cognitiveservices.azure.com/voice-live/realtime?api-key=***&model=gpt-realtime' failed: Error during WebSocket handshake: Unexpected response code: 404
```

### Root Cause
The Voice Live API WebSocket URL was missing the required `api-version` parameter and using incorrect parameter names.

### Fix
Changed the WebSocket URL construction from:
```javascript
// WRONG
const wsUrl = `${wsEndpoint}voice-live/realtime?api-key=${VOICELIVE_API_KEY}&model=${VOICELIVE_MODEL}`;
```

To:
```javascript
// CORRECT
const wsUrl = `${wsEndpoint}voice-live/realtime?api-key=${VOICELIVE_API_KEY}&api-version=${VOICELIVE_API_VERSION}&deployment=${VOICELIVE_MODEL}`;
```

### Lesson
Always include the `api-version` parameter when calling Azure Cognitive Services APIs. The parameter name for the model is `deployment`, not `model`.

---

## Issue 2: ICE Connection Going to "disconnected" State

### Symptom
```
ICE state: checking
Received track: video
Received track: audio
ICE state: disconnected
```
The WebRTC connection would receive tracks but then immediately disconnect.

### Root Cause
The SDP offer was being sent before ICE gathering completed. Azure Voice Live requires the complete SDP with all ICE candidates included.

### Fix
Added a wait for ICE gathering to complete before sending the SDP:

```javascript
// Wait for ICE gathering to complete
await new Promise((resolve) => {
  if (pc.iceGatheringState === 'complete') {
    resolve();
  } else {
    const checkState = () => {
      if (pc.iceGatheringState === 'complete') {
        pc.removeEventListener('icegatheringstatechange', checkState);
        resolve();
      }
    };
    pc.addEventListener('icegatheringstatechange', checkState);
    // Timeout after 5 seconds
    setTimeout(resolve, 5000);
  }
});

console.log('ICE gathering complete, sending SDP');
// Now send the SDP with all candidates
```

### Lesson
When using WebRTC with Azure Voice Live, wait for ICE gathering to complete (`iceGatheringState === 'complete'`) before sending the client SDP. This ensures all relay candidates are included in the offer.

---

## Issue 3: Microphone Audio Sample Rate

### Symptom
Audio not being captured at the correct sample rate for Azure Voice Live (24kHz).

### Root Cause
Browsers do NOT support `sampleRate: 24000` as a `getUserMedia` constraint. The Web Audio API captures at the device's native sample rate (typically 44100Hz or 48000Hz).

### Fix
Capture audio at native rate and resample manually to 24kHz:

```javascript
// Capture at native rate (browsers don't support sampleRate constraint)
const stream = await navigator.mediaDevices.getUserMedia({
  audio: {
    channelCount: 1,
    echoCancellation: true,
    noiseSuppression: true,
    autoGainControl: true
  }
});

const audioContext = new AudioContext();
const nativeSampleRate = audioContext.sampleRate; // e.g., 48000

// Resample function
function resampleAudio(inputData, fromSampleRate, toSampleRate) {
  if (fromSampleRate === toSampleRate) return inputData;
  const ratio = fromSampleRate / toSampleRate;
  const newLength = Math.round(inputData.length / ratio);
  const result = new Float32Array(newLength);
  for (let i = 0; i < newLength; i++) {
    const srcIndex = i * ratio;
    const floor = Math.floor(srcIndex);
    const ceil = Math.min(floor + 1, inputData.length - 1);
    const fraction = srcIndex - floor;
    result[i] = inputData[floor] * (1 - fraction) + inputData[ceil] * fraction;
  }
  return result;
}

// In audio processor
processor.onaudioprocess = (e) => {
  const inputData = e.inputBuffer.getChannelData(0);
  const resampled = resampleAudio(inputData, nativeSampleRate, 24000);
  const pcm16 = floatTo16BitPCM(resampled);
  sendAudio(pcm16);
};
```

### Lesson
Never assume browsers support arbitrary sample rates. Always capture at native rate and resample in JavaScript.

---

## Issue 4: Audio Feedback Loop Prevention

### Symptom
Potential audio feedback when the microphone captures the avatar's audio output.

### Fix
Use a silent gain node to keep the audio processor running without actual playback:

```javascript
const silentGain = audioContext.createGain();
silentGain.gain.value = 0;
processor.connect(silentGain);
silentGain.connect(audioContext.destination);
```

### Lesson
When capturing microphone audio that will be processed but not played back locally, use a zero-gain node to prevent feedback while keeping the audio graph active.

---

## Issue 5: Error State Not Clearing on Disconnect

### Symptom
Error messages (like "Failed to access microphone") persisted in the UI after disconnecting.

### Fix
Added `setError(null)` to the disconnect function:

```javascript
const disconnect = useCallback(() => {
  stopMicrophone();
  // ... cleanup code ...
  setIsConnected(false);
  setStatus('disconnected');
  setTranscript('');
  setAssistantText('');
  setError(null);  // Clear any errors
}, [stopMicrophone]);
```

### Lesson
Always reset all UI state (including error states) when returning to the initial/disconnected state.

---

## Issue 6: API Keys Exposure

### Symptom
Risk of exposing Azure API keys in frontend code.

### Fix
Created a backend proxy that holds the API keys and returns only the necessary connection URLs:

```javascript
// Backend (server.js)
app.get('/api/voicelive-config', (req, res) => {
  const wsUrl = `${wsEndpoint}voice-live/realtime?api-key=${VOICELIVE_API_KEY}&...`;
  res.json({ wsUrl });
});

// Frontend
const configResponse = await fetch('/api/voicelive-config');
const config = await configResponse.json();
const ws = new WebSocket(config.wsUrl);
```

### Lesson
Never hardcode API keys in frontend code. Use a backend proxy to securely manage credentials.

---

## Issue 7: ICE Token Format

### Symptom
Confusion about the ICE token response format from Azure.

### Observation
The Azure Speech Service ICE token endpoint returns:
```json
{
  "Urls": ["turn:relay.communication.microsoft.com:3478"],
  "Username": "...",
  "Password": "..."
}
```

Note the capitalized property names (`Urls`, `Username`, `Password`).

### Fix
Map the response correctly to WebRTC ICE server format:

```javascript
const iceServers = [{
  urls: iceData.Urls,      // Note: Azure returns "Urls" (capitalized)
  username: iceData.Username,
  credential: iceData.Password
}];
```

### Lesson
Pay attention to the exact casing of API response properties. Azure uses PascalCase for some responses.

---

## Debugging Tips

### 1. Add Comprehensive WebRTC Logging
```javascript
pc.onicecandidate = (event) => {
  console.log('ICE candidate:', event.candidate?.candidate || 'gathering complete');
};
pc.onicegatheringstatechange = () => {
  console.log('ICE gathering state:', pc.iceGatheringState);
};
pc.oniceconnectionstatechange = () => {
  console.log('ICE connection state:', pc.iceConnectionState);
};
pc.onconnectionstatechange = () => {
  console.log('Connection state:', pc.connectionState);
};
```

### 2. Log WebSocket Messages
```javascript
ws.onmessage = (event) => {
  const msg = JSON.parse(event.data);
  console.log('WS message:', msg.type);
  // Handle message...
};
```

### 3. Test with Playwright MCP Server
Use browser automation to systematically test:
- `browser_navigate` - Load the app
- `browser_click` - Interact with buttons
- `browser_console_messages` - Check for errors
- `browser_network_requests` - Verify API calls
- `browser_take_screenshot` - Visual verification

---

## Summary of Key Azure Voice Live Requirements

| Requirement | Details |
|-------------|---------|
| WebSocket URL | Must include `api-version` parameter |
| ICE Gathering | Must complete before sending SDP |
| Audio Format | PCM16 at 24kHz sample rate |
| SDP Format | Base64-encoded JSON of RTCSessionDescription |
| ICE Transport | Use `iceTransportPolicy: 'relay'` |
| Avatar Config | Include in `session.update` message |

---

## Issue 8: React StrictMode Causing Duplicate Connections

### Symptom
```
WebRTC connection is in connected state
```
Multiple duplicate WebSocket and WebRTC connections were being established, causing errors and connection instability.

### Root Cause
React 18's StrictMode intentionally double-invokes effects in development mode to help detect side effects. This caused `useEffect` hooks to run twice, establishing duplicate WebSocket and WebRTC connections.

### Fix
1. **Remove StrictMode** from `index.js`:
```javascript
// Before
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// After
root.render(<App />);
```

2. **Add ref guards** to prevent duplicate setup:
```javascript
const avatarSetupStartedRef = useRef(false);
const audioSetupStartedRef = useRef(false);
const isCleanedUpRef = useRef(false);

const setupAvatar = async (avatarConfig) => {
  if (avatarSetupStartedRef.current || isCleanedUpRef.current) {
    console.log('Avatar setup already started or cleaned up, skipping');
    return;
  }
  avatarSetupStartedRef.current = true;
  // ... setup code
};
```

### Lesson
For components with WebSocket/WebRTC connections, either disable StrictMode or implement robust guards using refs to prevent duplicate connection attempts. Refs persist across re-renders and are the correct tool for tracking "already started" state.

---

## Issue 9: Avatar Character + Style Availability

### Symptom
```
Error: Avatar with character [harry] and style [casual-sitting] not found.
Error: Avatar with character [lisa] and style [graceful-standing] not found.
```
Many avatar character and style combinations result in errors.

### Root Cause
Azure Voice Live avatars have limited character+style combinations available, which vary by region. Not all combinations advertised in documentation are available.

### Fix
Implement graceful fallback to audio-only mode:
```javascript
if (msg.error?.message?.includes('not found')) {
  addLog(`Error: ${msg.error.message}`, 'error');
  addLog('Falling back to audio-only mode', 'info');
  setAvatarStatus('Audio-only mode (avatar not available)');
  setIsAudioOnly(true);
  // Continue with audio-only session
}
```

### Working Combinations (Sweden Central)
| Character | Working Styles |
|-----------|---------------|
| Lisa | casual-sitting |
| Others | Varies by region |

### Lesson
Always implement fallback modes for Azure AI services. Avatar availability is region-specific and may change. Test combinations in your target region and provide graceful degradation.

---

## Issue 10: WebSocket Close Codes

### Observation
Different WebSocket close codes indicate different scenarios:
- `1000` - Normal closure (user clicked End Session)
- `1006` - Abnormal closure (connection error, avatar unavailable)

### Fix
Handle both cases appropriately:
```javascript
ws.onclose = (event) => {
  addLog(`WebSocket closed: ${event.code}`, 'info');
  if (event.code === 1006 && !isCleanedUpRef.current) {
    // Unexpected closure - may need to show error
    addLog('Connection closed unexpectedly', 'warning');
  }
};
```

### Lesson
Monitor WebSocket close codes to distinguish between intentional disconnections and errors. Code 1006 often indicates server-side rejection or network issues.

---

## Playwright MCP Testing Checklist

### Verified Features
| Feature | Test Method | Result |
|---------|-------------|--------|
| Start Avatar Session | Click button, check logs | ✅ |
| Avatar Video Display | Screenshot + snapshot | ✅ |
| Text Chat Send | Type + click Send | ✅ |
| AI Responses | Check message bubbles | ✅ |
| End Session | Click button, verify return to config | ✅ |
| Character Selection | select_option tool | ✅ |
| Style Selection | select_option tool | ✅ |
| Voice Selection | select_option tool | ✅ |
| Audio-only Fallback | Test unavailable combo | ✅ |
| Error Handling | Test invalid combos | ✅ |
| Connection Logging | Visual + snapshot check | ✅ |

### Useful Playwright MCP Commands
```
browser_navigate - Load the application
browser_click - Interact with buttons
browser_type - Enter text in fields
browser_select_option - Choose dropdown values
browser_snapshot - Get page accessibility tree
browser_take_screenshot - Visual verification
browser_console_messages - Check for JS errors
```

### Lesson
Use Playwright MCP for systematic end-to-end testing. The combination of snapshots (for element refs) and screenshots (for visual verification) provides comprehensive coverage.

---

## References

- [Azure Voice Live API Documentation](https://learn.microsoft.com/azure/ai-services/speech-service/)
- [WebRTC API](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)
- [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [React StrictMode](https://react.dev/reference/react/StrictMode)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samelhousseini) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
