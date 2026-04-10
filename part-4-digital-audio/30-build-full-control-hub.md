<!--
  CHAPTER: 30
  TITLE: "Build: The Full Control Hub"
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 29
  KEY_TOPICS: multi-room audio, Snapcast, touchscreen, source switching, analog input, ADC, mDNS, REST API, hardware controls, motorized potentiometer, relay mute, Arduino, ESP32, OTA updates, system architecture
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 30: Build — The Full Control Hub

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 29 | Difficulty: Advanced

You've built a Pi streamer that plays music (Chapter 28) and processes it through an active crossover with room correction (Chapter 29). It has physical controls, a display, and sounds excellent. Now we're going to turn it into the nerve center of your entire audio system.

This chapter evolves the Pi streamer into a full audio control hub: multi-room playback across your home, a touchscreen interface for detailed control, source switching between streaming and physical inputs (turntable, CD player, line-in), a REST API for automation, and -- most importantly -- a set of physical master controls that work at the hardware level, not just the software level. If the Pi crashes, locks up, or catches fire, you can still adjust the volume, mute the output, and switch sources. These controls operate through a dedicated microcontroller that manages analog switches and relays independently of the Pi.

This is the culmination of Part 4. By the end, you'll have a system that a non-technical household member can walk up to and use without knowing what a Raspberry Pi is, while simultaneously giving you -- the engineer who built it -- full programmatic control over every parameter.

### In This Chapter
- Multi-room audio with Snapcast
- Touchscreen interface design and implementation
- Source switching: analog and digital inputs
- Network integration: mDNS, web interface, REST API
- Physical master controls: volume, source, mute, power
- Hardware-level control with a dedicated microcontroller
- System architecture diagram
- OTA updates and maintenance

### Related Chapters
- [Ch 26: Streaming Architectures and Protocols](26-streaming-architectures-protocols.md) -- multi-room protocols
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- the streamer we're evolving
- [Ch 29: Build: Active Crossover and Room Correction](29-build-active-crossover-room-correction.md) -- the DSP pipeline
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) -- the amplifiers in the system
- [Ch 32: Enclosure Design and Fabrication](../part-5-pcb-professional/32-enclosure-design-fabrication.md) -- building the case for this hub
- [Ch 38: Complete Build: Home Hi-Fi](../part-6-system-design/38-complete-build-home-hifi.md) -- the overall audio system this hub controls
- [Ch 39: Complete Build: Party PA](../part-6-system-design/39-complete-build-party-pa.md) -- integrating the hub into a whole-home system

---

## 1. Multi-Room Audio with Snapcast

### 1.1 The Architecture

Snapcast turns your main Pi into a server that broadcasts audio to client Pis in other rooms. Every client plays the same audio in perfect synchronization.

```
┌─────────────────────────────────┐
│         MAIN PI (Hub)           │
│                                 │
│  Music Source → MPD → Snapcast  │──── Ethernet/WiFi ────┐
│                      Server     │                        │
│         │                       │                        │
│         ▼                       │                        │
│    CamillaDSP → DAC → Amp      │                        │
│    (local playback)             │                        │
└─────────────────────────────────┘                        │
                                                           │
        ┌──────────────────────────────────────────────────┤
        │                      │                           │
        ▼                      ▼                           ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────────┐
│  KITCHEN Pi   │    │  BEDROOM Pi   │    │  OFFICE Pi        │
│  Snapclient   │    │  Snapclient   │    │  Snapclient       │
│  → DAC → Amp  │    │  → DAC → Amp  │    │  → DAC → Amp      │
│  → Speakers   │    │  → Speakers   │    │  → Speakers        │
└───────────────┘    └───────────────┘    └───────────────────┘
```

### 1.2 Snapcast Server Setup (Main Pi)

```bash
# Install Snapcast server
sudo apt install snapserver

# Configure the Snapcast server
sudo tee /etc/snapserver.conf << 'EOF'
[http]
enabled = true
bind_to_address = 0.0.0.0
port = 1780
doc_root = /usr/share/snapserver/snapweb

[tcp]
enabled = true
bind_to_address = 0.0.0.0
port = 1705

[stream]
# Source from MPD via a named pipe
source = pipe:///tmp/snapfifo?name=default&sampleformat=48000:16:2
# Additional sources (optional):
# source = librespot:///librespot?name=Spotify&devicename=MultiRoom&bitrate=320
# source = airplay:///shairport-sync?name=AirPlay&port=5000
EOF
```

Configure MPD to output to the Snapcast pipe:

```bash
# Add to /etc/mpd.conf (or configure in moOde):
audio_output {
    type        "fifo"
    name        "Snapcast"
    path        "/tmp/snapfifo"
    format      "48000:16:2"
    mixer_type  "software"
}
```

Now MPD's audio goes to both the local DAC (for the main room) and to Snapcast (for all rooms).

```bash
# Enable and start the server
sudo systemctl enable --now snapserver
```

### 1.3 Snapcast Client Setup (Satellite Pis)

Each satellite Pi needs:
- Raspberry Pi (even a Pi Zero 2 W works for clients)
- DAC HAT (HiFiBerry DAC+ Standard is fine for satellite rooms)
- Snapclient software
- (Optional) Physical controls from Chapter 28

```bash
# On each satellite Pi:
sudo apt install snapclient

# Configure the client to point to the main Pi
sudo tee /etc/default/snapclient << 'EOF'
SNAPCLIENT_OPTS="--host 192.168.1.100 --soundcard hw:sndrpihifiberry"
EOF

# Enable and start
sudo systemctl enable --now snapclient
```

Replace `192.168.1.100` with your main Pi's IP address (or hostname if mDNS is working).

### 1.4 Zone Control

Snapcast supports per-client volume and mute via its JSON-RPC API and the built-in web interface (Snapweb):

```bash
# Access the Snapweb UI:
# http://main-pi-ip:1780

# Or use the JSON-RPC API:
# Get server status
curl -s http://main-pi-ip:1780/jsonrpc -d '{"id":1,"jsonrpc":"2.0","method":"Server.GetStatus"}' | python3 -m json.tool

# Set volume for a specific client
curl -s http://main-pi-ip:1780/jsonrpc -d '{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "Client.SetVolume",
  "params": {"id": "client-uuid", "volume": {"percent": 75, "muted": false}}
}'
```

### 1.5 Independent Zone Playback

Snapcast by default plays the same audio to all clients. For different music in different rooms, you need multiple streams:

```bash
# In snapserver.conf, define multiple streams:
[stream]
source = pipe:///tmp/snapfifo_main?name=Living%20Room
source = pipe:///tmp/snapfifo_kitchen?name=Kitchen
source = pipe:///tmp/snapfifo_bedroom?name=Bedroom
```

Each stream is a separate "group" in Snapcast. Clients can be assigned to different groups via the API or web UI. You'd run separate MPD instances (or separate librespot/shairport-sync instances) feeding each pipe.

This gets complex. For most homes, synchronized whole-house audio from a single source is what you actually want. Add independent zones only if you genuinely need different music in different rooms simultaneously.

---

## 2. Touchscreen Interface

### 2.1 Hardware

The **Raspberry Pi Official 7" Touchscreen Display** ($70) connects directly to the Pi via a DSI ribbon cable and provides 800x480 resolution. It's well-supported in Raspberry Pi OS and moOde.

Alternatives:
- **Hyperpixel 4" (800x480)**: Smaller, uses GPIO (conflict with DAC HAT -- needs I2C only)
- **HDMI touchscreens (7", 10")**: More resolution options, connected via HDMI + USB for touch

For this build, we use the official 7" display. It doesn't conflict with the DAC HAT (uses DSI, not GPIO).

### 2.2 Approach: Local Web App

You're a software engineer. You know how to build web apps. Rather than learning a Pi-specific GUI toolkit, we'll build the touchscreen interface as a **local web application** that runs in a fullscreen kiosk browser.

This approach has massive advantages:
- You use technologies you already know (HTML, CSS, JavaScript)
- The same interface is accessible from any device on the network
- Updates are instant (edit files, refresh browser)
- Responsive design works across the 7" touch display and phone/tablet browsers
- No compile step, no framework lock-in

### 2.3 UI Design

The touchscreen UI shows:

```
┌──────────────────────────────────────────────────────┐
│                                                      │
│  ┌──────────┐   Title of Current Track               │
│  │          │   Artist Name                          │
│  │  Album   │   Album Name                           │
│  │   Art    │                                        │
│  │          │   prev  play  next     volume slider   │
│  └──────────┘   ─────────────────── 2:34 / 4:12      │
│                                                      │
│  ┌──────────────────────────────────────────────────┐│
│  │  Source: Spotify  │  EQ: Room Corrected  │ DSP   ││
│  └──────────────────────────────────────────────────┘│
│                                                      │
│  Zones:  Living Room: 85%    Kitchen: 60%            │
│          Bedroom: OFF        Office: 45%             │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### 2.4 Backend: Flask API

The web app needs a backend to interface with MPD, Snapcast, and CamillaDSP. A simple Flask server handles this:

```python
#!/usr/bin/env python3
"""
Pi Hub Control API
Flask server providing REST endpoints for the touchscreen UI.
"""

from flask import Flask, jsonify, request, send_from_directory
from mpd import MPDClient
import subprocess
import json
import os

app = Flask(__name__, static_folder="/opt/pihub/web/static")

MPD_HOST = "localhost"
MPD_PORT = 6600
SNAPCAST_HOST = "localhost"
SNAPCAST_PORT = 1780
CAMILLADSP_HOST = "localhost"
CAMILLADSP_PORT = 1234
ALSA_MIXER = "Digital"


def get_mpd():
    """Get a connected MPD client."""
    client = MPDClient()
    client.timeout = 5
    client.connect(MPD_HOST, MPD_PORT)
    return client


# --- Now Playing ---

@app.route("/api/now-playing")
def now_playing():
    """Get current track info and playback state."""
    try:
        client = get_mpd()
        status = client.status()
        song = client.currentsong()
        client.close()
        client.disconnect()

        return jsonify({
            "title": song.get("title", "Unknown"),
            "artist": song.get("artist", "Unknown"),
            "album": song.get("album", ""),
            "state": status.get("state", "stop"),
            "elapsed": float(status.get("elapsed", 0)),
            "duration": float(status.get("duration", 0)),
            "volume": get_volume(),
        })
    except Exception as e:
        return jsonify({"error": str(e)}), 500


# --- Transport ---

@app.route("/api/transport/<action>", methods=["POST"])
def transport(action):
    """Play, pause, next, previous."""
    try:
        client = get_mpd()
        if action == "play":
            client.play()
        elif action == "pause":
            client.pause()
        elif action == "toggle":
            status = client.status()
            if status.get("state") == "play":
                client.pause(1)
            else:
                client.play()
        elif action == "next":
            client.next()
        elif action == "previous":
            client.previous()
        client.close()
        client.disconnect()
        return jsonify({"ok": True})
    except Exception as e:
        return jsonify({"error": str(e)}), 500


# --- Volume ---

def get_volume():
    try:
        result = subprocess.run(
            ["amixer", "sget", ALSA_MIXER],
            capture_output=True, text=True, timeout=2
        )
        for line in result.stdout.split("\n"):
            if "%" in line:
                start = line.index("[") + 1
                end = line.index("%")
                return int(line[start:end])
    except Exception:
        pass
    return 50


@app.route("/api/volume", methods=["GET"])
def volume_get():
    return jsonify({"volume": get_volume()})


@app.route("/api/volume", methods=["POST"])
def volume_set():
    vol = request.json.get("volume", 50)
    vol = max(0, min(100, int(vol)))
    subprocess.run(["amixer", "sset", ALSA_MIXER, f"{vol}%"],
                    capture_output=True, timeout=2)
    return jsonify({"volume": vol})


# --- Zones (Snapcast) ---

@app.route("/api/zones")
def zones():
    """Get Snapcast zone information."""
    try:
        import urllib.request
        req = urllib.request.Request(
            f"http://{SNAPCAST_HOST}:{SNAPCAST_PORT}/jsonrpc",
            data=json.dumps({
                "id": 1, "jsonrpc": "2.0",
                "method": "Server.GetStatus"
            }).encode(),
            headers={"Content-Type": "application/json"}
        )
        with urllib.request.urlopen(req, timeout=3) as resp:
            data = json.loads(resp.read())
            return jsonify(data.get("result", {}))
    except Exception as e:
        return jsonify({"error": str(e)}), 500


# --- DSP Control ---

@app.route("/api/dsp/status")
def dsp_status():
    """Get CamillaDSP status."""
    try:
        from camilladsp import CamillaClient
        cdsp = CamillaClient(CAMILLADSP_HOST, CAMILLADSP_PORT)
        cdsp.connect()
        state = cdsp.general.state()
        rate = cdsp.rate.capture()
        signal = cdsp.levels.capture_rms()
        cdsp.disconnect()
        return jsonify({
            "state": str(state),
            "capture_rate": rate,
            "signal_rms": signal,
        })
    except Exception as e:
        return jsonify({"error": str(e)}), 500


@app.route("/api/dsp/config", methods=["POST"])
def dsp_load_config():
    """Load a CamillaDSP configuration preset."""
    config_name = request.json.get("config", "default")
    config_path = f"/etc/camilladsp/{config_name}.yml"
    if not os.path.exists(config_path):
        return jsonify({"error": f"Config not found: {config_name}"}), 404
    try:
        from camilladsp import CamillaClient
        cdsp = CamillaClient(CAMILLADSP_HOST, CAMILLADSP_PORT)
        cdsp.connect()
        cdsp.config.set_file_path(config_path)
        cdsp.general.reload()
        cdsp.disconnect()
        return jsonify({"ok": True, "config": config_name})
    except Exception as e:
        return jsonify({"error": str(e)}), 500


# --- Static files (the web UI) ---

@app.route("/")
def index():
    return send_from_directory("/opt/pihub/web", "index.html")


@app.route("/<path:path>")
def static_files(path):
    return send_from_directory("/opt/pihub/web", path)


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5001, debug=False)
```

### 2.5 Frontend: The Web UI

The frontend is a single-page web app. Here's a minimal but functional starting point. Note that we use `textContent` for safe DOM updates:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pi Audio Hub</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background: #1a1a2e;
            color: #eee;
            height: 100vh;
            overflow: hidden;
            user-select: none;
            -webkit-user-select: none;
        }
        .container { padding: 16px; height: 100vh; display: flex; flex-direction: column; }

        /* Now Playing */
        .now-playing {
            display: flex; gap: 16px; align-items: center;
            padding: 12px; background: #16213e; border-radius: 12px;
            margin-bottom: 12px;
        }
        .album-art {
            width: 100px; height: 100px; background: #0f3460;
            border-radius: 8px; flex-shrink: 0;
            display: flex; align-items: center; justify-content: center;
            font-size: 40px;
        }
        .track-info { flex: 1; min-width: 0; }
        .track-title { font-size: 18px; font-weight: 700; white-space: nowrap;
                        overflow: hidden; text-overflow: ellipsis; }
        .track-artist { font-size: 14px; color: #a0a0b0; margin-top: 4px; }

        /* Transport Controls */
        .transport {
            display: flex; justify-content: center; gap: 20px;
            padding: 12px 0; align-items: center;
        }
        .transport button {
            background: none; border: 2px solid #444; color: #eee;
            width: 48px; height: 48px; border-radius: 50%;
            font-size: 20px; cursor: pointer; display: flex;
            align-items: center; justify-content: center;
        }
        .transport button:active { background: #333; }
        .transport .play-btn { width: 56px; height: 56px; border-color: #e94560; }

        /* Volume Slider */
        .volume-section {
            display: flex; align-items: center; gap: 12px;
            padding: 8px 16px; background: #16213e; border-radius: 8px;
            margin-bottom: 12px;
        }
        .volume-label { font-size: 12px; color: #888; min-width: 32px; }
        .volume-slider { flex: 1; height: 6px; -webkit-appearance: none;
                         background: #333; border-radius: 3px; outline: none; }
        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none; width: 20px; height: 20px;
            background: #e94560; border-radius: 50%; cursor: pointer;
        }

        /* Zones */
        .zones {
            display: grid; grid-template-columns: 1fr 1fr; gap: 8px;
            flex: 1; overflow-y: auto;
        }
        .zone {
            background: #16213e; border-radius: 8px; padding: 10px;
            display: flex; flex-direction: column; gap: 4px;
        }
        .zone-name { font-size: 13px; font-weight: 600; }
        .zone-volume { font-size: 11px; color: #888; }

        /* Status Bar */
        .status-bar {
            display: flex; justify-content: space-between;
            padding: 8px 0; font-size: 11px; color: #666;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="now-playing">
            <div class="album-art" id="albumArt"></div>
            <div class="track-info">
                <div class="track-title" id="trackTitle">Loading...</div>
                <div class="track-artist" id="trackArtist">--</div>
            </div>
        </div>

        <div class="transport">
            <button id="prevBtn">Prev</button>
            <button class="play-btn" id="playBtn">Play</button>
            <button id="nextBtn">Next</button>
        </div>

        <div class="volume-section">
            <span class="volume-label">Vol</span>
            <input type="range" class="volume-slider" id="volumeSlider"
                   min="0" max="100" value="50">
            <span class="volume-label" id="volumeValue">50%</span>
        </div>

        <div class="zones" id="zones">
            <!-- Populated by JavaScript -->
        </div>

        <div class="status-bar">
            <span id="sourceInfo">Source: --</span>
            <span id="dspInfo">DSP: --</span>
            <span id="formatInfo">--</span>
        </div>
    </div>

    <script>
        // Transport button event listeners
        document.getElementById("prevBtn").addEventListener("click", function() {
            transport("previous");
        });
        document.getElementById("playBtn").addEventListener("click", function() {
            transport("toggle");
        });
        document.getElementById("nextBtn").addEventListener("click", function() {
            transport("next");
        });
        document.getElementById("volumeSlider").addEventListener("input", function() {
            setVolume(this.value);
        });

        async function fetchJson(url, opts) {
            const res = await fetch(url, opts);
            return res.json();
        }

        async function updateNowPlaying() {
            try {
                const data = await fetchJson("/api/now-playing");
                document.getElementById("trackTitle").textContent = data.title;
                document.getElementById("trackArtist").textContent = data.artist;
                document.getElementById("volumeSlider").value = data.volume;
                document.getElementById("volumeValue").textContent = data.volume + "%";
                document.getElementById("playBtn").textContent =
                    data.state === "play" ? "Pause" : "Play";
            } catch (e) { /* retry next cycle */ }
        }

        async function transport(action) {
            await fetchJson("/api/transport/" + action, { method: "POST" });
            setTimeout(updateNowPlaying, 200);
        }

        var volumeTimeout;
        async function setVolume(val) {
            document.getElementById("volumeValue").textContent = val + "%";
            clearTimeout(volumeTimeout);
            volumeTimeout = setTimeout(async function() {
                await fetchJson("/api/volume", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ volume: parseInt(val) })
                });
            }, 50);
        }

        // Poll for updates
        setInterval(updateNowPlaying, 1000);
        updateNowPlaying();
    </script>
</body>
</html>
```

### 2.6 Kiosk Mode

Configure the Pi to boot directly into the fullscreen web UI:

```bash
# Install Chromium (usually pre-installed on Pi OS)
sudo apt install chromium-browser unclutter

# Create an autostart entry for kiosk mode
mkdir -p /home/pi/.config/autostart
tee /home/pi/.config/autostart/kiosk.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=Pi Audio Hub Kiosk
Exec=/bin/bash -c "sleep 5 && unclutter -idle 0.5 & chromium-browser --noerrdialogs --disable-infobars --kiosk http://localhost:5001"
X-GNOME-Autostart-enabled=true
EOF
```

The `unclutter` utility hides the mouse cursor after 0.5 seconds of inactivity -- cleaner for a touchscreen.

---

## 3. Source Switching

### 3.1 The Problem

Your audio hub needs to play more than network streams. You want to connect:
- **Turntable** (with phono preamp): Analog line-level input
- **CD player**: Digital (S/PDIF) or analog input
- **TV**: Digital (HDMI ARC, optical) or analog input
- **Streaming services**: Network input (already handled)

### 3.2 Analog Input: ADC for the Pi

To get analog audio *into* the Pi for processing by CamillaDSP, you need an ADC (analog-to-digital converter). Options:

**USB ADC** (simplest):
- **Behringer UCA202/UCA222**: Cheap (~$30), stereo line input, adequate quality for vinyl digitization. 16-bit/48 kHz.
- **Focusrite Scarlett Solo**: Better quality (~$120), 24-bit/192 kHz, proper preamp. More than you need but excellent.
- **miniDSP USBStreamer**: If you already have this for the multi-channel output (Chapter 29), it has 2 analog inputs.

**I2S ADC HAT**:
- **IQaudIO Codec Zero**: Combined ADC + DAC on one HAT (~$20). Good for simple input/output. Uses WM8731 codec.
- **Fe-Pi Audio Z V2**: Stereo I2S ADC + DAC HAT (~$25).

**Configuration for a USB ADC**:

```bash
# Verify the ADC is recognized
arecord -l
# card 3: UCA222 [UCA222], device 0: USB Audio [USB Audio]

# Test recording
arecord -D hw:UCA222 -f S16_LE -r 48000 -c 2 test.wav
# Play something into the line input and verify the recording
aplay test.wav
```

### 3.3 CamillaDSP with Analog Input

To process a live analog input through CamillaDSP (for EQ, crossover, room correction):

```yaml
# CamillaDSP config for analog input
devices:
  samplerate: 48000
  chunksize: 1024
  capture:
    type: Alsa
    channels: 2
    device: "hw:UCA222"     # The USB ADC
    format: S16LE
  playback:
    type: Alsa
    channels: 4             # Multi-channel output for active crossover
    device: "hw:USBStreamer"
    format: S32LE

# ... same filters and pipeline as Chapter 29 ...
```

The key difference: `capture.device` points to the ADC instead of the ALSA loopback. The rest of the pipeline (EQ, crossover, room correction) is identical.

### 3.4 Source Selection in Software

You need a way to switch CamillaDSP's input between the ALSA loopback (network streams) and the USB ADC (analog input). Options:

**Option A: Multiple CamillaDSP configs**: Create separate YAML configs for each source, and switch configs via the websocket API.

```bash
# Switch to analog input
curl -X POST http://localhost:5001/api/dsp/config \
  -H "Content-Type: application/json" \
  -d '{"config": "analog_input"}'

# Switch back to network streaming
curl -X POST http://localhost:5001/api/dsp/config \
  -H "Content-Type: application/json" \
  -d '{"config": "network_input"}'
```

**Option B: ALSA mixer routing**: Use `alsaloop` or a custom ALSA configuration to route different inputs to the same ALSA loopback device. CamillaDSP always reads from the loopback, and you switch which source feeds it.

Option A is simpler and more reliable. The config reload takes about 200ms -- a brief silence during the switch.

### 3.5 Digital Input: S/PDIF

For S/PDIF input (from a CD player or TV), you need a S/PDIF receiver HAT:

- **HiFiBerry Digi+ I/O**: S/PDIF input and output via optical (TOSLINK) and coaxial. ~$35. Receives digital audio directly into the Pi's I2S bus.

Configure it as an ALSA capture device in CamillaDSP, just like the USB ADC example above.

---

## 4. Network Integration

### 4.1 mDNS Service Discovery

Your hub should be discoverable on the network without knowing its IP address. Avahi (Linux's mDNS/DNS-SD implementation) handles this automatically -- it's why you can access `http://moode.local`.

For the control API, advertise a custom service:

```bash
# Create an Avahi service file
sudo tee /etc/avahi/services/pihub.service << 'EOF'
<?xml version="1.0" standalone="no"?>
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
  <name replace-wildcards="yes">Pi Audio Hub (%h)</name>
  <service>
    <type>_http._tcp</type>
    <port>5001</port>
    <txt-record>path=/api</txt-record>
  </service>
</service-group>
EOF

sudo systemctl restart avahi-daemon
```

Now any device on the network can discover your hub via mDNS -- the `_http._tcp` service type is browsable by standard discovery tools.

### 4.2 The REST API

The Flask server from Section 2.4 already provides a REST API. Here's the complete endpoint summary:

| Endpoint | Method | Description |
|---|---|---|
| `/api/now-playing` | GET | Current track, state, volume |
| `/api/transport/<action>` | POST | play, pause, toggle, next, previous |
| `/api/volume` | GET | Current volume |
| `/api/volume` | POST | Set volume `{"volume": 75}` |
| `/api/zones` | GET | Snapcast zone status |
| `/api/dsp/status` | GET | CamillaDSP state, signal levels |
| `/api/dsp/config` | POST | Load a DSP preset `{"config": "name"}` |

This API is accessible from any device on the network. You can build automations:

```python
# Example: Turn down the music at 10 PM (run via cron)
import requests
requests.post("http://pihub.local:5001/api/volume",
              json={"volume": 25})
```

```python
# Example: Switch to vinyl input when the turntable starts
# (detect with a simple audio level threshold on the ADC)
import requests
requests.post("http://pihub.local:5001/api/dsp/config",
              json={"config": "analog_input"})
```

### 4.3 Home Automation Integration

The REST API makes integration with home automation systems straightforward:

- **Home Assistant**: Use the REST sensor and REST command integrations to add your hub as a media player entity
- **Node-RED**: HTTP request nodes can control the hub
- **Apple HomeKit**: Use homebridge with an HTTP plugin to expose the hub as a HomeKit accessory
- **Custom scripts**: `curl` from any shell script or cron job

---

## 5. Physical Master Controls: The Non-Negotiable

### 5.1 The Design Principle

This is the most important section of this chapter. Read it carefully.

The physical controls in Chapter 28 -- the rotary encoder and buttons -- are software-controlled. They read GPIO, run a Python script, and send commands to ALSA or MPD. If the Python script crashes, or if MPD hangs, or if the Pi kernel panics, those controls stop working. You're sitting in silence, or worse, sitting with music blasting that you can't turn down.

**The master controls in this section operate at the hardware level.** They work even if the Pi is completely dead. They use a dedicated microcontroller (Arduino or ESP32) to control analog switches and relays that sit in the *analog signal path* between the DAC output and the amplifier input.

### 5.2 The Control Architecture

```
DAC Output (analog)
    |
    v
+------------------------------------------------------+
|              HARDWARE CONTROL BOARD                   |
|                                                       |
|  +----------+    +--------------+    +------------+   |
|  | Motorized |    | Analog Mux/  |    | Mute Relay |   |
|  | Pot or    |--->| Source       |--->| (normally  |-->  To Amplifier
|  | VCA       |    | Selector     |    |  closed)   |   |
|  +-----+----+    +------+-------+    +------+-----+   |
|        |                |                    |         |
|  +-----+----------------+--------------------+-----+   |
|  |            ESP32 Microcontroller                |   |
|  |                                                 |   |
|  |  Reads: Volume encoder, Source switch, Mute btn |   |
|  |  Controls: Motorized pot, Analog mux, Relay     |   |
|  |  Communicates: Serial/I2C to Raspberry Pi       |   |
|  +-------------------------------------------------+   |
|                                                       |
|  Physical Controls (front panel):                     |
|  +----------+ +------------+ +------+ +----------+   |
|  | Volume   | | Source     | | Mute | | Power    |   |
|  | Encoder  | | Selector   | | Btn  | | Button   |   |
|  | (rotary) | | (rotary sw)| |      | |          |   |
|  +----------+ +------------+ +------+ +----------+   |
+------------------------------------------------------+
```

### 5.3 Why a Separate Microcontroller?

The ESP32 (or Arduino) serves as a hardware safety layer:

1. **It boots instantly**: No 30-second Linux boot. The ESP32 is running within milliseconds of power-on.
2. **It never crashes**: A simple control loop reading encoders, setting relay states, and controlling a volume attenuator is trivially reliable.
3. **It's independent**: If the Pi hangs, the ESP32 keeps working. Volume, mute, and source selection continue to function.
4. **It reports to the Pi**: The ESP32 communicates its state to the Pi over serial or I2C, so the touchscreen UI stays in sync.

### 5.4 Hardware Components

| Component | Part | Purpose | Cost |
|---|---|---|---|
| ESP32 DevKit | ESP32-WROOM-32 | Microcontroller | $5 |
| Motorized potentiometer | ALPS RK16812MG | Volume control (analog domain) | $15 |
| OR: VCA (voltage-controlled amplifier) | THAT2180 or SSM2164 | Volume control (cleaner, more precise) | $10 |
| Analog multiplexer | CD4053 or MAX4544 | Source selection (analog switching) | $2 |
| Signal relay | Omron G6K-2F-Y | Mute (physically breaks signal path) | $3 |
| Rotary encoder | KY-040 | Volume knob (reads rotation, sends to motorized pot/VCA) | $2 |
| Rotary switch | 4-position, panel mount | Source selector | $5 |
| Push button | Momentary, panel mount | Mute | $2 |
| Push button | Latching or momentary | Power | $3 |

### 5.5 Volume Control: Motorized Potentiometer

A **motorized pot** has a motor that can move the wiper. The ESP32 reads the rotary encoder and drives the motor to track it. The pot sits in the analog signal path between the DAC and the amplifier.

When the Pi sends a volume command (from the web UI or Spotify), it tells the ESP32 via serial, and the ESP32 motors the pot to match. The pot's physical position always reflects the actual volume.

If the Pi dies, you turn the encoder, and the ESP32 moves the pot directly. No software required.

```cpp
// ESP32 Arduino sketch (simplified)
// Volume control with motorized pot

#include <ESP32Encoder.h>

const int ENCODER_A = 14;
const int ENCODER_B = 27;
const int MOTOR_FWD = 25;  // H-bridge motor control
const int MOTOR_REV = 26;
const int POT_WIPER = 34;  // ADC input to read pot position
const int MUTE_RELAY = 16;
const int MUTE_BUTTON = 17;

ESP32Encoder encoder;
int targetVolume = 50;  // 0-100
bool muted = false;

void setup() {
    Serial.begin(115200);

    encoder.attachHalfQuad(ENCODER_A, ENCODER_B);
    encoder.setCount(50);

    pinMode(MOTOR_FWD, OUTPUT);
    pinMode(MOTOR_REV, OUTPUT);
    pinMode(MUTE_RELAY, OUTPUT);
    pinMode(MUTE_BUTTON, INPUT_PULLUP);

    digitalWrite(MUTE_RELAY, HIGH);  // Relay on = signal passes
}

void loop() {
    // Read encoder
    int encoderVal = constrain(encoder.getCount(), 0, 100);
    if (encoderVal != targetVolume) {
        targetVolume = encoderVal;
        driveMotorToTarget();
        reportToPi();
    }

    // Read mute button
    if (digitalRead(MUTE_BUTTON) == LOW) {
        delay(50);  // Debounce
        if (digitalRead(MUTE_BUTTON) == LOW) {
            muted = !muted;
            digitalWrite(MUTE_RELAY, muted ? LOW : HIGH);
            reportToPi();
            while (digitalRead(MUTE_BUTTON) == LOW) delay(10);  // Wait for release
        }
    }

    // Check for commands from Pi over Serial
    if (Serial.available()) {
        String cmd = Serial.readStringUntil('\n');
        handlePiCommand(cmd);
    }

    delay(10);
}

void driveMotorToTarget() {
    int currentPos = map(analogRead(POT_WIPER), 0, 4095, 0, 100);
    int diff = targetVolume - currentPos;

    if (abs(diff) < 2) {
        // Close enough -- stop motor
        digitalWrite(MOTOR_FWD, LOW);
        digitalWrite(MOTOR_REV, LOW);
        return;
    }

    if (diff > 0) {
        digitalWrite(MOTOR_FWD, HIGH);
        digitalWrite(MOTOR_REV, LOW);
    } else {
        digitalWrite(MOTOR_FWD, LOW);
        digitalWrite(MOTOR_REV, HIGH);
    }

    // Run motor briefly, then re-check
    delay(20);
    digitalWrite(MOTOR_FWD, LOW);
    digitalWrite(MOTOR_REV, LOW);
}

void handlePiCommand(String cmd) {
    // Commands from Pi: "VOL:75", "MUTE:1", "MUTE:0"
    if (cmd.startsWith("VOL:")) {
        targetVolume = constrain(cmd.substring(4).toInt(), 0, 100);
        encoder.setCount(targetVolume);
        driveMotorToTarget();
    } else if (cmd.startsWith("MUTE:")) {
        muted = cmd.substring(5).toInt() == 1;
        digitalWrite(MUTE_RELAY, muted ? LOW : HIGH);
    }
}

void reportToPi() {
    // Send state to Pi over Serial (Pi reads with a Python script)
    Serial.printf("STATE:VOL=%d,MUTE=%d\n", targetVolume, muted ? 1 : 0);
}
```

### 5.6 Mute: Hardware Relay

The mute button controls a signal relay that physically interrupts the analog signal path. When the relay is de-energized, the signal is cut. No software involvement. This is the panic button -- if anything goes wrong, press mute and the speakers go silent, instantly.

Use a **signal relay** (not a power relay). Signal relays like the Omron G6K series are designed for audio-frequency signals with low contact resistance and minimal distortion. A standard power relay would work but may introduce more contact noise.

### 5.7 Source Selector: Analog Multiplexer

A physical rotary switch on the front panel selects the input source. The ESP32 reads the switch position and controls an analog multiplexer (CD4053 or similar) that routes the selected input to the volume control.

```
Input Sources:
  +-- DAC Output (from Pi streamer)
  +-- Turntable (via phono preamp)
  +-- CD Player (line out)
  +-- Aux (3.5mm jack)
        |
        v
  +--------------------+
  | Analog Mux         |
  | (CD4053)           |---> Volume Control --> Mute Relay --> Amp
  | Controlled by      |
  | ESP32 based on     |
  | rotary switch      |
  +--------------------+
```

Even without the Pi, turning the physical source selector switch changes which input feeds the amplifier.

### 5.8 Pi-to-ESP32 Communication

The ESP32 connects to the Pi via USB serial (simplest) or I2C. A small Python daemon on the Pi reads the ESP32's state reports and sends commands:

```python
#!/usr/bin/env python3
"""
Pi Hub — ESP32 Bridge
Reads hardware control state from ESP32 and syncs with software.
"""

import serial
import time
import subprocess

SERIAL_PORT = "/dev/ttyUSB0"
BAUD_RATE = 115200
ALSA_MIXER = "Digital"

def main():
    ser = serial.Serial(SERIAL_PORT, BAUD_RATE, timeout=1)
    print(f"ESP32 bridge started on {SERIAL_PORT}")

    while True:
        try:
            if ser.in_waiting:
                line = ser.readline().decode().strip()
                if line.startswith("STATE:"):
                    handle_state(line[6:])
        except Exception as e:
            print(f"Error: {e}")
            time.sleep(1)

def handle_state(state_str):
    """Parse ESP32 state and sync with Pi software."""
    params = dict(item.split("=") for item in state_str.split(","))

    vol = int(params.get("VOL", 50))

    # Sync ALSA volume with hardware volume
    subprocess.run(["amixer", "sset", ALSA_MIXER, f"{vol}%"],
                    capture_output=True, timeout=2)

if __name__ == "__main__":
    main()
```

> **What happens if...** the Pi crashes while music is playing at full volume? Without hardware controls, you're stuck with deafening music until you unplug the Pi or the amplifier. With the hardware mute relay, you press the mute button and the signal is physically cut -- the relay doesn't need the Pi, the ESP32, or even electricity (it fails to mute state when de-powered). This is not a theoretical concern. Linux kernel panics happen. SD card corruption happens. A physical mute button is the audio equivalent of an emergency stop button on a machine tool.

---

## 6. System Architecture

### 6.1 Complete Signal Flow Diagram

```
+-------------------------------------------------------------------------+
|                          PI AUDIO HUB                                   |
|                                                                         |
|  +------- NETWORK SOURCES ------+   +---- PHYSICAL SOURCES ----+       |
|  | Spotify Connect (librespot)  |   | Turntable (phono preamp) |       |
|  | AirPlay 2 (shairport-sync)  |   | CD Player (line out)     |       |
|  | DLNA/UPnP (MPD)             |   | TV (optical/analog)      |       |
|  | Local library (MPD + NAS)   |   | Aux (3.5mm)              |       |
|  +----------+-------------------+   +-----------+--------------+       |
|             |                                    |                      |
|             v                                    v                      |
|  +------------------+              +---------------------+              |
|  |  ALSA Loopback   |              |  USB ADC / S/PDIF   |              |
|  |  (virtual card)  |              |  HAT (digitize)     |              |
|  +--------+---------+              +----------+----------+              |
|           |                                    |                        |
|           +--------------+---------------------+                        |
|                          |                                              |
|                          v                                              |
|  +------------------------------------------------------+              |
|  |                   CamillaDSP                         |              |
|  |  +----------+ +-----------+ +----------+ +-------+  |              |
|  |  | Room     | | Crossover | | Per-     | | Time  |  |              |
|  |  | Correct. |>| (LR4)    |>| Driver EQ|>| Align |  |              |
|  |  +----------+ +-----------+ +----------+ +-------+  |              |
|  +----------------------------+-------------------------+              |
|                             |                                          |
|                             v                                          |
|  +--------------------------------------------+                        |
|  |         Multi-Channel DAC                  |                        |
|  |  (miniDSP USBStreamer / HiFiBerry DAC8x)   |                        |
|  +-----+--------+--------+--------+-----------+                        |
|        |        |        |        |                                     |
|  +-----+--------+--------+--------+---------------------+              |
|  |              HARDWARE CONTROL BOARD (ESP32)           |              |
|  |  Source Mux > Volume (Motorized Pot) > Mute Relay     |              |
|  |  [Phys. controls: Knob, Switch, Mute Btn, Power]      |              |
|  +-------+----------+----------+----------+--------------+              |
|          |          |           |            |                          |
|          v          v           v            v                          |
|        Ch 1       Ch 2       Ch 3         Ch 4                         |
|                                                                         |
|  +--- Snapcast Server ----------------------------------------+        |
|  |  > Satellite Pi #1 (Kitchen) > DAC > Amp > Speakers       |        |
|  |  > Satellite Pi #2 (Bedroom) > DAC > Amp > Speakers       |        |
|  |  > Satellite Pi #3 (Office)  > DAC > Amp > Speakers       |        |
|  +------------------------------------------------------------+        |
|                                                                         |
|  +--- User Interfaces -------------------------------------------+     |
|  |  7" Touchscreen (local kiosk browser)                         |     |
|  |  Web UI (any browser: phone, tablet, laptop)                  |     |
|  |  Physical controls (always work, even if Pi is down)          |     |
|  |  REST API (home automation, custom scripts)                   |     |
|  +---------------------------------------------------------------+     |
+-------------------------------------------------------------------------+
         |          |           |            |
         v          v           v            v
      Amp 1      Amp 2       Amp 3        Amp 4
   (Class D)   (Class D)  (Class D)    (Class D)
         |          |           |            |
         v          v           v            v
      Woofer     Woofer     Tweeter      Tweeter
      (Left)     (Right)    (Left)       (Right)
```

---

## 7. OTA Updates and Maintenance

### 7.1 System Updates

```bash
# Update the base OS and packages
sudo apt update && sudo apt upgrade -y

# Update CamillaDSP (download latest release from GitHub)
# Check https://github.com/HEnquist/camilladsp/releases

# Update the control scripts
# (Pull from your git repo, or scp from your development machine)
```

### 7.2 Backup Configuration

```bash
# Backup all configurations
mkdir -p ~/backups
tar czf ~/backups/pihub-config-$(date +%Y%m%d).tar.gz \
    /etc/camilladsp/ \
    /opt/pistreamer/ \
    /opt/pihub/ \
    /etc/mpd.conf \
    /etc/snapserver.conf \
    /etc/systemd/system/pistreamer-*.service \
    /etc/systemd/system/pihub-*.service

# Copy to NAS or another machine
scp ~/backups/pihub-config-*.tar.gz nas:/backups/
```

### 7.3 Monitoring System Health

A simple health check script:

```python
#!/usr/bin/env python3
"""Pi Hub health monitor. Run via cron every 5 minutes."""

import subprocess
import os

def check_service(name):
    result = subprocess.run(
        ["systemctl", "is-active", name],
        capture_output=True, text=True
    )
    return result.stdout.strip() == "active"

services = [
    "mpd",
    "snapserver",
    "camilladsp",
    "pistreamer-volume",
    "pistreamer-transport",
    "pistreamer-display",
]

for svc in services:
    status = "OK" if check_service(svc) else "FAILED"
    print(f"  {svc}: {status}")
    if status == "FAILED":
        # Auto-restart failed services
        subprocess.run(["sudo", "systemctl", "restart", svc])

# Check CPU temperature
with open("/sys/class/thermal/thermal_zone0/temp") as f:
    temp = float(f.read()) / 1000
print(f"  CPU temp: {temp:.1f}C")
if temp > 80:
    print("  WARNING: CPU temperature is high!")

# Check disk usage
stat = os.statvfs("/")
free_gb = (stat.f_bavail * stat.f_frsize) / (1024**3)
print(f"  Disk free: {free_gb:.1f} GB")
if free_gb < 1:
    print("  WARNING: Low disk space!")
```

### 7.4 SD Card Longevity

MicroSD cards have a limited number of write cycles. To extend the life of your Pi's SD card:

```bash
# Reduce unnecessary writes by mounting /tmp and /var/log as tmpfs
# Add to /etc/fstab:
tmpfs /tmp tmpfs defaults,noatime,nosuid,nodev,size=100m 0 0
tmpfs /var/log tmpfs defaults,noatime,nosuid,nodev,size=50m 0 0

# Disable swap (reduces writes, and with 4GB RAM you don't need it for audio)
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
```

For maximum reliability, consider booting from a USB SSD instead of an SD card. Pi 4 and Pi 5 support USB boot natively.

---

## 8. What You've Built

This is the complete system. Let's step back and appreciate what you have:

**A networked audio hub** that receives music from Spotify, Apple Music (via AirPlay), your local FLAC library, a turntable, a CD player, or any line-level source.

**A DSP engine** that applies room correction, active crossovers, per-driver equalization, and time alignment in real time, running on a $55 single-board computer.

**Multi-room audio** synchronized across every room in your house, each with its own volume control.

**A touchscreen interface** and web UI accessible from any device, with full transport control, zone management, and DSP preset switching.

**A REST API** that lets you automate anything -- turn down the volume at bedtime, switch to the turntable when you want vinyl, adjust EQ presets for different genres.

**Hardware-level physical controls** that work even if every piece of software on the Pi crashes. The volume knob turns, the mute button mutes, and the source selector selects -- through analog switches and relays controlled by a dedicated microcontroller that runs independently of the Pi.

**Total hardware cost** for the main hub: ~$300-400 (Pi + DAC + multi-channel output + ESP32 control board + display + misc components). Each satellite room: ~$80-100 (Pi + DAC + case).

This system competes with commercial multi-room audio solutions costing $5,000-10,000 -- and exceeds them in flexibility, repairability, and the satisfaction of having built it yourself. The commercial systems don't give you DSP crossovers, room correction, or hardware-level physical controls. Yours does, because you built it.

> **What happens if...** you bring an audiophile friend over and they ask what gear you're using? Tell them it's a custom DSP crossover with room correction, active amplification, and multi-room sync. Show them the measurement before and after correction. Play their favorite test track. Then tell them the DAC cost $45 and the entire system runs on a Raspberry Pi. Watch their expression. This is what it means to be a 100x audio builder.

---

## Summary

The full control hub evolves the Pi streamer from Chapters 28-29 into a complete audio system nerve center. Snapcast provides synchronized multi-room playback to satellite Pis throughout the house. A 7" touchscreen running a web app in kiosk mode gives detailed local control, while the same web interface is accessible from any device on the network. The Flask backend exposes a REST API for programmatic control and home automation integration.

Source switching handles both network streams (via ALSA loopback) and physical inputs (turntable, CD, aux via USB ADC), routed through CamillaDSP for identical room correction and crossover processing regardless of source. Multiple CamillaDSP configurations provide switchable presets for different listening scenarios.

The hardware control layer -- an ESP32 managing a motorized potentiometer, analog multiplexer, and signal relay -- ensures that volume, source selection, and mute work at the analog level, independent of the Pi's software state. These controls are the non-negotiable safety net: if everything else fails, you can still control your audio system.

Service discovery (mDNS/Avahi), automated health monitoring, configuration backups, and SD card longevity measures keep the system reliable for long-term use. The complete system architecture diagram shows every signal path from every source through DSP to every output, with physical controls intercepting at the hardware level.
