<!--
  CHAPTER: 26
  TITLE: Streaming Architectures and Protocols
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 25
  KEY_TOPICS: DLNA/UPnP, AirPlay 2, Spotify Connect, Chromecast Audio, Roon/RAAT, Volumio, moOde, library management, multi-room audio, Snapcast, latency, Ethernet vs WiFi
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 26: Streaming Architectures and Protocols

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 25 | Difficulty: Intermediate

You now understand what digital audio *is* -- samples, bits, DAC architectures, the whole signal path from file to analog voltage. But there's a gap between "I have a FLAC file on a hard drive" and "music is coming out of my speakers." That gap is filled by protocols, servers, and software -- the plumbing that moves audio data from wherever it lives to wherever it needs to play.

This is your territory. You're a software engineer. Networking, client-server architecture, service discovery, APIs -- you've built these things. What's different here is the real-time constraint: audio must arrive at the DAC in a continuous, uninterrupted stream at precisely the right rate. A 200ms hiccup that would be imperceptible in a web request produces an audible dropout in music playback. Buffer underrun is the enemy. Everything in this chapter -- the protocols, the software choices, the network architecture -- is designed to prevent that.

By the end of this chapter, you'll understand the major streaming protocols, know which player software to install on your Pi, and have a clear picture of how everything fits together. In Chapter 28, we build it.

### In This Chapter
- How network audio works: source, transport, renderer
- Streaming protocols: DLNA/UPnP, AirPlay 2, Spotify Connect, Chromecast, Roon/RAAT
- Music server software: Volumio, moOde Audio, HiFiBerry OS
- Library management and metadata
- High-resolution streaming services
- Multi-room audio: Snapcast, AirPlay 2 multi-room, Roon
- Latency considerations across protocols
- Wired vs wireless: why Ethernet wins

### Related Chapters
- [Ch 25: Digital Audio Fundamentals](25-digital-audio-fundamentals.md) -- the digital audio that these protocols transport
- [Ch 27: DSP Fundamentals for Audio](27-dsp-fundamentals.md) -- processing that can be applied at the renderer
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- installing and configuring this software
- [Ch 30: Build: The Full Control Hub](30-build-full-control-hub.md) -- multi-room and source switching
- [Ch 39: Complete Build: Party PA](../part-6-system-design/39-complete-build-party-pa.md) -- whole-home audio architecture

---

## 1. Network Audio Architecture: The Three Roles

### 1.1 Source, Transport, Renderer

Every network audio system has three logical components:

**Source (Server/Media Server)**: Where the audio data lives. This could be:
- A NAS (network-attached storage) with your FLAC library
- A streaming service (Spotify, Tidal, Qobuz)
- A phone or computer running a music app
- A microphone input being broadcast live

**Transport (Protocol)**: How the audio data moves from source to renderer. This is the protocol layer -- DLNA, AirPlay, Spotify Connect, etc. Each protocol defines how devices discover each other, how they negotiate capabilities, and how the data stream is formatted and delivered.

**Renderer (Player/Endpoint)**: The device that receives the audio data, decodes it, and outputs it to a DAC. In our case, this is the Raspberry Pi with its DAC HAT.

Some systems separate these cleanly (DLNA has distinct server, renderer, and controller roles). Others blur the lines (Spotify Connect makes the Pi both the renderer and the source, since it streams directly from Spotify's servers). Understanding which component does what helps you troubleshoot when things don't work.

### 1.2 The Control Plane vs The Data Plane

This should feel familiar from your software background. In network audio:

**Control plane**: Discovery (finding devices on the network), session management (selecting what to play, where to play it), transport control (play, pause, skip, volume). This is typically low-bandwidth, latency-tolerant, and often uses HTTP/REST, mDNS, or SSDP.

**Data plane**: The actual audio stream. This is high-bandwidth (CD quality is ~1.4 Mbps, hi-res can be 9+ Mbps), latency-sensitive, and typically uses RTP, HTTP streaming, or direct TCP connections.

The control plane is what your phone app interacts with. The data plane is what feeds the DAC. In a well-designed system, the data plane works independently of the control plane -- if your phone loses WiFi, the music keeps playing because the data path is separate.

---

## 2. Streaming Protocols

### 2.1 DLNA/UPnP: The Default for Local Libraries

**What it is**: DLNA (Digital Living Network Alliance) is a set of guidelines built on top of UPnP (Universal Plug and Play) that lets devices share and play media over a local network. It defines three roles:

- **Media Server**: Hosts content and makes it browsable (your NAS running MiniminiDLNA, or your Pi running a DLNA server)
- **Media Renderer**: Receives and plays audio streams (your Pi streamer)
- **Media Controller**: The user interface that tells the renderer what to play (your phone running BubbleUPnP, or a web interface)

**How it works**:
1. Devices announce themselves on the network using SSDP (Simple Service Discovery Protocol) -- multicast UDP on port 1900
2. The controller discovers servers and renderers
3. The controller browses the server's content (over HTTP/XML)
4. The controller tells the renderer to play a specific URL from the server
5. The renderer fetches the audio data directly from the server (the controller is not in the data path)

**Protocol stack**: SSDP for discovery → HTTP/SOAP for control → HTTP for media streaming. The audio stream is typically a direct HTTP GET of the audio file or a transcoded stream.

**Strengths**:
- Open standard, widely supported
- Works with any DLNA-compatible app on any platform
- The controller (phone) can disconnect after starting playback -- the data flows directly from server to renderer

**Weaknesses**:
- UPnP is notoriously finicky -- device discovery sometimes fails, especially across subnets or VLANs
- The XML/SOAP control protocol is verbose and fragile
- Gapless playback support is inconsistent across implementations
- No native multi-room synchronization
- DRM content is not supported (streaming services can't use it)

**For your Pi**: Both Volumio and moOde include DLNA/UPnP renderer support out of the box. You'll use this for playing your local library from a NAS or any DLNA server.

**Server software for your NAS**:
- **MiniminiDLNA (ReadyMedia)**: Lightweight, does one thing well. `sudo apt install minidlna` on any Linux box.
- **Jellyfin**: Full media server with web UI, transcoding, user management. Heavier but much more capable.
- **Plex**: Similar to Jellyfin but proprietary. Has its own streaming protocol in addition to DLNA.

### 2.2 AirPlay 2: Apple's Protocol

**What it is**: Apple's proprietary protocol for streaming audio (and video) from Apple devices to compatible receivers. AirPlay 2 (released 2018) added multi-room synchronization, buffering improvements, and Siri integration.

**How it works**:
1. The receiver advertises itself via mDNS/Bonjour (service type `_airplay._tcp` and `_raop._tcp`)
2. The Apple device discovers the receiver and shows it in the AirPlay menu
3. The user selects the receiver
4. Audio is streamed using RTSP (Real-Time Streaming Protocol) for session management and RTP (Real-Time Protocol) for audio data
5. Audio is typically sent as Apple Lossless (ALAC) -- lossless, 16-bit/44.1 kHz

**On Linux**: **shairport-sync** is the open-source AirPlay receiver for Linux. It supports AirPlay 2 (with some configuration), outputs to ALSA, and handles the mDNS advertisement automatically. It's mature, well-maintained, and works excellently on the Pi.

```bash
# Install shairport-sync (on Raspberry Pi OS)
sudo apt install shairport-sync

# It auto-starts as a service and advertises via Avahi (Linux mDNS)
# Your Pi will appear as an AirPlay target on any Apple device on the network
```

**Strengths**:
- Seamless integration with Apple devices (iPhone, iPad, Mac)
- AirPlay 2 multi-room with tight synchronization (within ~1ms)
- Lossless audio quality
- Background playback -- the data streams from the source, not through the controlling device (for supported apps)

**Weaknesses**:
- Apple ecosystem only (Android users need third-party workarounds)
- Protocol is proprietary -- shairport-sync reverse-engineers it, so there can be compatibility hiccups after Apple OS updates
- Volume control is handled differently from native ALSA volume (can be confusing)

**For your Pi**: Enable shairport-sync as a plugin in Volumio or moOde. Any Apple device on your network will see the Pi as an AirPlay speaker. This is one of the most-used streaming methods in practice.

### 2.3 Spotify Connect: Native Spotify Integration

**What it is**: Spotify's built-in protocol for streaming to external devices. Unlike Bluetooth streaming (where your phone decodes Spotify and sends the audio), Spotify Connect makes the renderer stream directly from Spotify's servers. Your phone is just the remote control.

**How it works**:
1. The Pi runs a Spotify Connect daemon (librespot, an open-source Rust implementation)
2. The daemon announces itself via mDNS (Zeroconf)
3. The Spotify app on your phone discovers the Pi as a playback device
4. When you select it, the Pi streams directly from Spotify's CDN
5. Your phone controls playback but isn't in the data path

**Audio quality**: Spotify Premium streams at 320 kbps Ogg Vorbis. As discussed in Chapter 25, this is perceptually transparent for virtually all listeners. Spotify's HiFi tier (lossless, FLAC-based) has been announced and re-announced multiple times -- check current status when you build.

**On Linux**: **librespot** is the open-source Spotify Connect implementation. It's written in Rust, is actively maintained, and works well on the Pi.

```bash
# librespot is typically installed as a plugin in Volumio/moOde
# Manual installation:
cargo install librespot
# Or use a pre-built binary from the releases page

# Run it:
librespot --name "Pi Streamer" --backend alsa --device hw:0 --bitrate 320
```

**Strengths**:
- Works with the Spotify app on any platform (iOS, Android, Windows, Mac, web)
- Data streams directly from Spotify, not through your phone
- Extremely reliable -- it's Spotify's own protocol
- Your phone can leave the network after selecting the device

**Weaknesses**:
- Requires Spotify Premium
- Limited to Spotify's catalog (no local library support)
- Audio quality is lossy (320 kbps Vorbis)
- Spotify controls the protocol -- if they change it, librespot needs to catch up

**For your Pi**: This will probably be your most-used streaming method. Enable the Spotify Connect plugin in Volumio/moOde. It just works.

### 2.4 Chromecast Audio / Google Cast

**What it is**: Google's protocol for streaming to external devices. Originally launched with Chromecast hardware, the protocol can be implemented by any device.

**How it works**: Similar to Spotify Connect -- the controlling device (phone, computer) tells the renderer what to play, and the renderer streams the content directly. The protocol uses mDNS for discovery, CASTV2 for session management, and direct streaming for audio data.

**On Linux**: **go-chromecast** or **catt** can turn the Pi into a Cast target, but support is less mature than AirPlay or Spotify Connect. Google hasn't officially documented the Cast protocol for third-party receiver implementations, so open-source implementations are reverse-engineered.

**For your Pi**: Chromecast audio support is the least reliable of the major protocols on Linux. If you're in the Google ecosystem, it's worth trying, but don't count on it as your primary streaming method. Spotify Connect and AirPlay cover most use cases.

### 2.5 Roon / RAAT

**What it is**: Roon is a premium music management and playback system. It runs a **Roon Core** (typically on a powerful computer, NAS, or dedicated Roon server) that manages your local library and integrates streaming services (Tidal, Qobuz). Playback devices run **Roon Bridge**, which receives audio from the Core and outputs to the DAC.

The audio transport protocol is **RAAT** (Roon Advanced Audio Transport), a proprietary protocol designed specifically for audio with features like:
- Bit-perfect delivery
- Signal path transparency (Roon shows you every DSP operation applied to the audio)
- Tight synchronization for multi-room
- Automatic sample rate matching

**On Linux**: Roon Bridge is available as a Linux binary for ARM (works on Pi).

```bash
# Install Roon Bridge on Pi
curl -O http://download.roonlabs.com/builds/RoonBridge_linuxarmv8.tar.bz2
tar xjf RoonBridge_linuxarmv8.tar.bz2
cd RoonBridge
./start.sh
```

**Strengths**:
- Best-in-class library management (metadata, album identification, credits, reviews)
- Signal path transparency -- you know exactly what's happening to your audio
- Excellent multi-room with tight sync
- Integrates local library with Tidal/Qobuz seamlessly
- Built-in DSP (parametric EQ, convolution, headroom management)

**Weaknesses**:
- Expensive: Roon subscription is ~$15/month or ~$830 lifetime
- Requires a separate Roon Core machine (the Pi is not powerful enough to run Core)
- Proprietary and closed -- if Roon goes away, so does your setup
- Overkill if you just want to play Spotify

**For your Pi**: If you're serious about library management and are willing to invest in the Roon ecosystem, install Roon Bridge alongside your other protocols. The Pi can be a Roon endpoint while simultaneously running Spotify Connect and AirPlay.

### 2.6 Protocol Comparison

| Protocol | Audio Quality | Multi-Room | Phone as Remote | Local Library | Streaming Services | Open Source |
|---|---|---|---|---|---|---|
| DLNA/UPnP | Any (file native) | No (basic) | Yes (third-party app) | Yes | No | Yes |
| AirPlay 2 | Lossless (ALAC) | Yes (tight sync) | Yes (Apple only) | Via iTunes/Music | Apple Music | No (shairport-sync reverse-engineered) |
| Spotify Connect | 320 kbps Vorbis | Yes (Spotify groups) | Yes (any platform) | No | Spotify only | No (librespot reverse-engineered) |
| Chromecast | Varies | Yes (Cast groups) | Yes (any platform) | Via Cast apps | Various | No (partially reverse-engineered) |
| Roon/RAAT | Bit-perfect | Yes (tight sync) | Yes (Roon app) | Yes | Tidal, Qobuz | No |

---

## 3. Music Server Software for the Pi

### 3.1 The Landscape

You don't have to build everything from scratch. Several complete music server distributions exist for the Pi that bundle the OS, player software, streaming protocol support, and a web interface into a single SD card image. Here are the serious options.

### 3.2 Volumio

**What it is**: A Debian-based distribution purpose-built for music playback on the Pi (and other SBCs). It has a polished web UI, a plugin system for extending functionality, and a focus on "just works" setup.

**Key features**:
- Clean, modern web interface accessible from any browser
- Plugin ecosystem: Spotify Connect, AirPlay, Tidal Connect, YouTube Music, DSP plugins
- DLNA/UPnP renderer built in
- Supports I2S DAC HATs out of the box
- OTA updates

**Free vs Premium**: The base system is free. "Volumio Premium" (subscription) adds features like Tidal/Qobuz integration, multi-room, hi-res streaming, and MyVolumio cloud access. For basic Spotify + AirPlay + local library playback, the free tier is sufficient.

**Installation**:
```bash
# Download the Pi image from volumio.com
# Write to SD card (use Raspberry Pi Imager or dd)
# Boot the Pi -- it creates a WiFi hotspot for initial config
# Connect to http://volumio.local from any browser
# Select your DAC HAT, configure WiFi, done
```

**Best for**: Users who want a polished, low-friction experience. If you want to spend time listening to music rather than configuring software, Volumio is the right choice.

### 3.3 moOde Audio

**What it is**: A fully free, open-source audio player distribution for the Pi. Maintained by Tim Curtis, it's built on Raspberry Pi OS with MPD (Music Player Daemon) as the audio engine.

**Key features**:
- Completely free and open source (GPL)
- Extremely configurable -- every audio parameter is exposed
- Built-in CamillaDSP integration (critical for Chapter 29)
- Spotify Connect via librespot
- AirPlay via shairport-sync
- DLNA/UPnP renderer
- Bluetooth receiver
- Parametric EQ built into the UI
- Detailed system information (sample rate, bit depth, signal path)

**Installation**:
```bash
# Download from moodeaudio.org
# Write to SD card
# Boot the Pi
# Connect to http://moode.local or http://moode
# Configure DAC HAT in Audio Config
# Enable Spotify, AirPlay, etc. in the respective config pages
```

**Best for**: Tinkerers and builders. moOde gives you more control than Volumio, its CamillaDSP integration is invaluable for the active crossover build in Chapter 29, and it's completely free. This is what I recommend for this guide's builds.

### 3.4 HiFiBerry OS

**What it is**: A minimal OS from HiFiBerry (the DAC HAT manufacturer) designed specifically for their hardware. It's the simplest option -- almost no configuration required if you're using a HiFiBerry board.

**Key features**:
- Dead simple setup with HiFiBerry DAC HATs
- AirPlay, Spotify Connect, Bluetooth, DLNA built in
- Web interface for basic control
- Roon Ready (certified Roon endpoint)

**Best for**: If you just want a streaming endpoint and don't plan to do DSP, room correction, or heavy customization. It's the "appliance" option.

### 3.5 Software Comparison

| Feature | Volumio (Free) | Volumio (Premium) | moOde Audio | HiFiBerry OS |
|---|---|---|---|---|
| Price | Free | ~$7/month | Free | Free |
| Open Source | Partially | No | Yes (GPL) | No |
| Spotify Connect | Plugin | Plugin | Built-in | Built-in |
| AirPlay | Plugin | Plugin | Built-in | Built-in |
| DLNA Renderer | Yes | Yes | Yes | Yes |
| Tidal/Qobuz | No | Yes | Community plugins | Yes |
| CamillaDSP | Limited | Limited | Excellent integration | No |
| Multi-Room | No | Yes (Multiroom plugin) | Via Snapcast | Limited |
| Custom GPIO Scripts | Possible | Possible | Straightforward | Difficult |
| Roon Ready | No | Yes | Via Roon Bridge | Yes (certified) |

**My recommendation**: **moOde Audio** for this guide. It gives you the most flexibility for the builds in Chapters 28-30, its CamillaDSP integration is essential for the active crossover project, and GPIO scripting for physical controls is well-documented. Volumio is the better choice if you want a more polished experience and don't plan to do DSP work.

### 3.6 Building Your Own: The DIY Software Stack

If you don't want a pre-built distribution, you can build a streamer from stock Raspberry Pi OS. This gives you maximum control but requires more setup:

```bash
# Start with Raspberry Pi OS Lite (no desktop, minimal footprint)

# Install the audio stack
sudo apt install mpd mpc alsa-utils

# Install streaming protocol support
sudo apt install shairport-sync    # AirPlay
cargo install librespot             # Spotify Connect (or use a pre-built binary)

# Install CamillaDSP (from GitHub releases)
wget https://github.com/HEnquist/camilladsp/releases/download/v2.0.3/camilladsp-linux-aarch64.tar.gz

# Configure MPD
sudo nano /etc/mpd.conf
# Set music_directory, audio output, database, etc.

# Configure the DAC HAT
# Add to /boot/config.txt:
dtoverlay=hifiberry-dacplus
# (or the appropriate overlay for your HAT)
```

This approach is more work but teaches you exactly how each component fits together. If something breaks, you know where to look because you built it. For this guide, we'll use moOde (which gives you all of this pre-configured) but knowing the underlying components helps you debug and customize.

### 3.7 Docker-Based Approach

If you prefer containerized services, you can run the streaming stack in Docker containers on the Pi:

```bash
# Docker on Pi
sudo apt install docker.io

# Run shairport-sync in a container
docker run -d --name airplay \
  --device /dev/snd \
  --net=host \
  mikebrady/shairport-sync

# Run librespot in a container
docker run -d --name spotify \
  --device /dev/snd \
  --net=host \
  librespot-docker:latest \
  --name "Pi Streamer" --bitrate 320
```

Docker adds overhead and complexity that's unnecessary for a dedicated audio appliance, but it's an option if you want to run other services on the same Pi and want isolation between them.

---

## 4. Library Management and Metadata

### 4.1 Local Library Storage

Your Pi streamer can play music from:
- **USB drive**: Plug a USB drive with FLAC files directly into the Pi. Simplest possible setup.
- **NAS (Network Attached Storage)**: A dedicated file server on your network. Mount it via SMB (Samba) or NFS. Both Volumio and moOde can mount network shares and scan them for music.
- **MicroSD card**: You can put music files on the same SD card as the OS, but this limits your storage and mixes data with the system.

For a serious library, **a NAS is the right answer**. A basic NAS (Synology DS223, QNAP TS-233, or just a Raspberry Pi with an external drive running Samba) costs $150-300 and gives you centralized storage accessible from every streamer in your house.

```bash
# Mount a NAS share on moOde/Volumio (via the web UI is easiest, but here's the manual way)
sudo mount -t cifs //nas-ip/music /mnt/NAS -o username=your_user,password=your_pass,vers=3.0

# Or add to /etc/fstab for automatic mounting at boot:
//nas-ip/music /mnt/NAS cifs username=your_user,password=your_pass,vers=3.0,_netdev 0 0
```

### 4.2 Metadata and Tagging

Good metadata makes your library usable. Bad metadata makes it a mess. Your player software (MPD under moOde, or Volumio's internal database) builds its library view from the metadata embedded in your audio files.

**Essential tags**: Artist, Album Artist, Album, Title, Track Number, Disc Number, Year, Genre.

**Tagging tools**:
- **MusicBrainz Picard**: Free, open source, excellent. Fingerprints your audio files and matches them against the MusicBrainz database. Automatically fills in metadata including album art.
- **beets**: Command-line music library manager (Python). Extremely powerful, scriptable, and automatable. `pip install beets` -- you're a Python user, you'll love this.
- **Mp3tag** (Windows) / **Kid3** (cross-platform): GUI taggers for manual correction.

```bash
# beets example: import and auto-tag your music library
pip install beets
beet config  # edit configuration
beet import /path/to/messy/music/folder
# beets will fingerprint, match, rename, and organize your files
```

### 4.3 Album Art

Album art is embedded in the audio file (as a tag) and/or stored as a separate image file in the album folder (typically `cover.jpg` or `folder.jpg`). Your player's web UI and the OLED display we'll build in Chapter 28 both display album art. MusicBrainz Picard and beets can automatically fetch and embed album art.

Recommended: embed art in each file AND keep a `cover.jpg` in each album folder. Belt and suspenders.

---

## 5. High-Resolution Streaming Services

### 5.1 The Landscape

If your music lives in the cloud rather than on a NAS, these are the services that offer high-quality streaming:

| Service | Max Quality | Format | Pi Integration | Price (approx) |
|---|---|---|---|---|
| Spotify Premium | 320 kbps Ogg Vorbis | Lossy | Excellent (librespot) | $11/month |
| Apple Music | Lossless (ALAC, up to 24/192) | Lossless | Via AirPlay | $11/month |
| Tidal | MQA*, FLAC (up to 24/192) | Lossless | Via Volumio Premium or Roon | $11-22/month |
| Qobuz | FLAC (up to 24/192) | Lossless | Via Volumio Premium or Roon | $13-18/month |
| Amazon Music HD | FLAC (up to 24/192) | Lossless | Limited (Chromecast) | $9/month |
| YouTube Music | 256 kbps AAC | Lossy | Community plugins | $11/month |

*MQA (Master Quality Authenticated) is a controversial lossy codec that Tidal marketed as "lossless." It's essentially a proprietary encoding that requires an MQA-compatible DAC for full decoding. Many in the audio community are skeptical of MQA's claims, and Tidal has been moving toward offering standard FLAC alongside MQA.

### 5.2 Does Hi-Res Streaming Matter?

In Chapter 25, we established that 16-bit/44.1 kHz captures the full audible range with dynamic range exceeding the capability of any listening environment. So does it matter if your streaming service sends 24-bit/96 kHz?

**The honest answer**: The extra resolution doesn't help. What *does* help is that hi-res releases are often mastered differently (more dynamically) than their CD counterparts. The audible improvement people attribute to "hi-res" is frequently the better mastering, not the higher sample rate or bit depth.

**Practical advice**: If you're choosing between Spotify Premium (320 kbps lossy) and Qobuz (lossless FLAC), the Qobuz version will sound subtly better in some cases -- but mostly because of mastering differences, not because of the format. Use whichever service has the catalog and interface you prefer. Don't pay a premium for "hi-res" based on the numbers alone.

---

## 6. Multi-Room Audio

### 6.1 The Concept

Multi-room audio means playing music on multiple speakers in different rooms, either:
- **Synchronized**: The same music plays in every room, in perfect time sync
- **Independent**: Different music in different rooms, controlled from one interface

Both require multiple endpoints (Pi streamers) on the network. The challenge is synchronization -- audio must play at exactly the same time from every speaker, or the overlap in doorways and hallways sounds like a chorus effect (and not the pleasant kind).

### 6.2 Snapcast: The Open-Source Solution

**Snapcast** is an open-source multi-room audio system that synchronizes playback across multiple clients with sub-millisecond precision. It's the best open-source option and integrates beautifully with MPD-based systems like moOde.

**Architecture**:
```
                          ┌── Snapcast Client (Pi #1 + DAC → Kitchen speakers)
Music Source → MPD → Snapcast Server ─── Snapcast Client (Pi #2 + DAC → Living room speakers)
                          └── Snapcast Client (Pi #3 + DAC → Bedroom speakers)
```

The Snapcast server receives audio from the music player (via a named pipe or TCP), timestamps each chunk, and sends it to all connected clients. Each client buffers the audio and plays it at the exact right time based on the timestamp, compensating for network latency.

**Setup on Pi (moOde)**:
```bash
# Install Snapcast server on the main Pi
sudo apt install snapserver

# Configure MPD to output to a pipe that Snapcast reads
# In /etc/snapserver.conf:
[stream]
source = pipe:///tmp/snapfifo?name=default

# In MPD config, add a pipe output:
audio_output {
    type    "fifo"
    name    "Snapcast"
    path    "/tmp/snapfifo"
    format  "48000:16:2"
    mixer_type "software"
}

# Install Snapcast client on each satellite Pi
sudo apt install snapclient

# Configure the client to point to the server
# In /etc/default/snapclient:
SNAPCLIENT_OPTS="--host server-ip-address"

# Start everything
sudo systemctl enable --now snapserver  # on the server Pi
sudo systemctl enable --now snapclient  # on each client Pi
```

**Synchronization quality**: Snapcast achieves sub-millisecond sync accuracy over Ethernet. Over WiFi, sync is still excellent (a few milliseconds) but depends on network quality. For most rooms, even 10ms of sync error is inaudible.

### 6.3 AirPlay 2 Multi-Room

If your household is Apple-centric, AirPlay 2 provides native multi-room. Each Pi running shairport-sync appears as a separate AirPlay speaker. From an iOS device, you can group speakers and control volume per room.

The synchronization in AirPlay 2 is handled by Apple's protocol, and shairport-sync implements it. Sync quality is good -- Apple has invested heavily in making multi-room AirPlay reliable.

### 6.4 Roon Multi-Room

Roon's multi-room is the premium option. The Roon Core manages all playback, and each Pi running Roon Bridge is an independently controlled zone. You can group zones for synchronized playback or play different music in each. Roon handles sample rate conversion, DSP, and volume leveling per zone.

### 6.5 Multi-Room Planning: How Many Zones?

Before you build a multi-room system, plan your zones. Each zone needs a Pi, a DAC, an amplifier, and speakers. The cost per zone is roughly $80-150 for the electronics plus whatever you spend on speakers.

A practical multi-room plan for a typical house:

| Zone | Pi | DAC | Amp | Speakers | Notes |
|---|---|---|---|---|---|
| Living Room (main) | Pi 4/5 | HiFiBerry DAC2 Pro | Class D (TPA3255) | DIY bookshelf from Part 2 | The primary listening position, full DSP |
| Kitchen | Pi Zero 2 W | HiFiBerry DAC+ Zero | Small Class D (TPA3116) | Compact speakers or ceiling-mount | Background music, doesn't need DSP |
| Bedroom | Pi 3B+ | JustBoom DAC HAT | Integrated amp | Small bookshelf speakers | Bedside listening, volume control matters |
| Patio/Deck | Pi Zero 2 W | USB DAC | Outdoor amp | Outdoor speakers (weather-rated) | Longest cable runs, needs weather protection for the Pi |

The living room runs the Snapcast server and all DSP processing. The satellite rooms are simple clients. You don't need a Pi 4 or Pi 5 for a Snapcast client -- a Pi Zero 2 W ($15) handles the client workload easily.

### 6.6 Multi-Room Comparison

| Feature | Snapcast | AirPlay 2 | Roon |
|---|---|---|---|
| Cost | Free | Free (shairport-sync) | ~$15/month + Core hardware |
| Sync Quality | Sub-ms (Ethernet) | ~1ms | Sub-ms |
| Per-Zone Volume | Yes | Yes | Yes |
| Different Music Per Zone | No (same source) | Yes | Yes |
| Source Flexibility | Any MPD source | Apple devices only | Local library + Tidal/Qobuz |
| Setup Complexity | Moderate | Easy | Easy (after Core setup) |

> **What happens if...** you set up multi-room with Snapcast and one zone is on WiFi while the others are on Ethernet? The WiFi zone will occasionally experience micro-dropouts (a few milliseconds of silence) during network congestion. These sound like tiny clicks or gaps. They don't affect the Ethernet zones. If you hear them, either move that zone to Ethernet or increase the Snapcast buffer size for the WiFi client (the `--latency` flag on snapclient).

---

## 7. Latency Considerations

### 7.1 Why Latency Matters

In streaming audio, latency is the delay between "something happens at the source" and "you hear it at the speakers." For pure music listening, latency is irrelevant -- you don't care if the music starts 500ms after you press play. But latency matters for:

- **Video sync**: If you're streaming audio from a TV or movie, even 50ms of audio delay is noticeable as a lip-sync error
- **Live monitoring**: If you're using the Pi as an audio processor for a live signal (like a turntable playing through DSP), you want minimal latency
- **Interactive control**: The delay between pressing "pause" on your phone and the music actually stopping

### 7.2 Typical Latencies

| Protocol/System | Typical Latency | Notes |
|---|---|---|
| ALSA direct playback | 5-50 ms | Depends on buffer configuration |
| Spotify Connect | 200-500 ms | Acceptable for music listening |
| AirPlay 2 | 500-2000 ms | Buffered for reliability, not for video sync |
| DLNA/UPnP | 200-1000 ms | Varies by implementation |
| Snapcast | 800-1200 ms | Intentionally buffered for synchronization |
| Roon/RAAT | 100-500 ms | Optimized but still buffered |
| Bluetooth (SBC) | 100-200 ms | Encoding + transmission |
| Bluetooth (aptX Low Latency) | 40 ms | Special codec, limited device support |

### 7.3 Latency and AV Sync

If you plan to use the Pi streamer as audio output for a TV, latency is a problem. Most streaming protocols introduce hundreds of milliseconds of delay, which causes visible lip-sync issues. Solutions:

- Use a direct HDMI or S/PDIF connection from the TV to the Pi (minimal latency)
- Use the TV's built-in lip-sync delay to compensate
- Don't use network streaming for video audio -- it's the wrong tool for the job

For your Pi music streamer, latency is a non-issue. You press play, and music starts within a second. Good enough.

---

## 8. Wired vs Wireless: The Network Question

### 8.1 Why Ethernet Wins

For a dedicated music streamer, Ethernet is unambiguously the right choice. Here's why:

**Reliability**: WiFi is a shared, contended medium. Other devices, microwave ovens, neighboring networks, and physical obstacles all compete for airtime. Ethernet is point-to-point, full-duplex, and doesn't contend with anything.

**Latency**: WiFi adds variable latency (jitter in the network sense). Ethernet latency is consistent and minimal. While this doesn't affect audio quality (the audio is buffered at the receiver), it affects transport reliability and reduces the chance of buffer underruns.

**Throughput**: Even basic 100 Mbps Ethernet provides ~70x the bandwidth needed for CD-quality audio and ~8x the bandwidth for 24/192 hi-res. WiFi can provide this bandwidth too, but it's variable and subject to interference.

**No dropouts**: WiFi dropouts are common -- a few lost packets during a congestion event, a momentary signal fade, a device roaming between access points. Each dropout can cause an audible glitch. Ethernet dropouts are essentially nonexistent on a functioning network.

### 8.2 When WiFi is Acceptable

That said, modern WiFi (802.11ac and 802.11ax) is perfectly adequate for music streaming in most homes. Audio is not a demanding workload -- 1.4 Mbps for CD quality is trivial compared to video streaming. If running Ethernet cable is impractical, WiFi works fine. Just be aware that:

- 5 GHz WiFi has less range but less interference than 2.4 GHz
- Place the Pi within reasonable range of your access point
- Don't use a Pi Zero W for streaming (its WiFi radio is weak)
- If you experience dropouts, Ethernet is the fix

### 8.3 Power over Ethernet (PoE)

If you're running Ethernet anyway, consider **PoE** (Power over Ethernet). A PoE HAT for the Pi lets you deliver both network and power over a single Ethernet cable. This means each Pi streamer needs only one cable run -- no power adapter needed at the speaker location.

```
PoE Switch ──── Single Ethernet Cable ──── Pi + PoE HAT + DAC HAT
   (power + data)                              (clean single-cable install)
```

The official Raspberry Pi PoE+ HAT works with Pi 4 and Pi 5. Note that stacking a PoE HAT and a DAC HAT requires either a GPIO header extender or a DAC that uses a different interface (USB). Some HAT DACs are designed for PoE stacking.

> **What happens if...** you use WiFi for your multi-room Snapcast setup and your neighbor fires up their microwave? If their microwave leaks 2.4 GHz interference (many do), and your Pi is on 2.4 GHz WiFi, you might get dropouts during their heating cycle. The music will stutter for 30 seconds while they heat their lunch. On Ethernet, you'd never know. This is the kind of annoyance that makes you run cable.

---

## 9. Putting It Together: Architecture for Your Pi Streamer

### 9.1 The Reference Architecture

Here's what we'll build in Chapter 28:

```
┌─────────────────────────────────────────────────────────┐
│                    YOUR NETWORK                         │
│                                                         │
│  ┌──────────┐    ┌──────────┐    ┌───────────────────┐  │
│  │  Phone   │    │   NAS    │    │  Spotify/Tidal/   │  │
│  │ (Control)│    │ (FLAC    │    │  Qobuz Servers    │  │
│  │          │    │  Library) │    │  (Internet)       │  │
│  └────┬─────┘    └─────┬────┘    └────────┬──────────┘  │
│       │                │                   │            │
│       │    ┌───────────┴───────────────────┘            │
│       │    │                                            │
│  ┌────┴────┴──────────────────────────────────────┐     │
│  │              Pi Streamer                        │     │
│  │  ┌──────────────────────────────────────────┐  │     │
│  │  │  moOde Audio / Volumio                   │  │     │
│  │  │  ┌───────────┐ ┌────────────┐ ┌───────┐ │  │     │
│  │  │  │ MPD       │ │ librespot  │ │shair- │ │  │     │
│  │  │  │ (DLNA,    │ │ (Spotify   │ │port-  │ │  │     │
│  │  │  │  local)   │ │  Connect)  │ │sync   │ │  │     │
│  │  │  └─────┬─────┘ └─────┬──────┘ │(Air-  │ │  │     │
│  │  │        └──────┬───────┘        │Play)  │ │  │     │
│  │  │               │                └───┬───┘ │  │     │
│  │  │         ┌─────┴────────────────────┘     │  │     │
│  │  │         ▼                                │  │     │
│  │  │   ALSA Audio Subsystem                   │  │     │
│  │  │         │                                │  │     │
│  │  │         ▼                                │  │     │
│  │  │   I2S → DAC HAT → Analog Out             │  │     │
│  │  └──────────────────────────────────────────┘  │     │
│  │                                                │     │
│  │  Physical Controls (GPIO)                      │     │
│  │  ┌─────────┐ ┌──────────┐ ┌──────┐            │     │
│  │  │ Rotary  │ │ Transport│ │ OLED │            │     │
│  │  │ Encoder │ │ Buttons  │ │Display│           │     │
│  │  │ (Volume)│ │ (Play/   │ │(Now  │            │     │
│  │  │         │ │  Skip)   │ │Playing│           │     │
│  │  └─────────┘ └──────────┘ └──────┘            │     │
│  └────────────────────────────────────────────────┘     │
│                          │                              │
└──────────────────────────┼──────────────────────────────┘
                           │
                           ▼
                    Your Amplifier → Your Speakers
```

### 9.2 The Software Stack

From bottom to top:

1. **Raspberry Pi OS** (via moOde Audio distribution): The Linux kernel with ALSA, I2S drivers, and device tree overlays for the DAC HAT
2. **MPD** (Music Player Daemon): The core audio engine -- decodes files, manages playlists, outputs to ALSA
3. **librespot**: Spotify Connect daemon -- appears as a Spotify device, streams to ALSA
4. **shairport-sync**: AirPlay receiver -- appears as an AirPlay speaker, streams to ALSA
5. **UPnP/DLNA renderer**: Receives streams from DLNA controllers and servers
6. **Web interface**: moOde's browser-based UI for configuration and playback control
7. **GPIO control scripts** (Chapter 28): Python services that read physical controls and control MPD/ALSA

### 9.3 The ALSA Architecture (For When Things Don't Work)

ALSA (Advanced Linux Sound Architecture) is the kernel-level audio subsystem on Linux. Every audio stream on the Pi -- whether from MPD, librespot, shairport-sync, or any other source -- goes through ALSA. Understanding its basics will save you hours of debugging.

**Key ALSA concepts**:

- **Sound cards**: Each audio device appears as a "card" in ALSA. Run `aplay -l` to list them. Your DAC HAT will be one card; the ALSA loopback (for CamillaDSP) will be another; a USB DAC would be yet another.
- **PCM devices**: Each card has one or more PCM (audio stream) devices. `hw:0,0` means card 0, device 0. `hw:sndrpihifiberry` uses a friendly name.
- **Mixer controls**: Volume levels, mute switches, and routing options. View them with `amixer` or `alsamixer`.
- **ALSA plugins**: Software layers that sit between the application and the hardware. `dmix` allows multiple applications to share a single output. `softvol` adds software volume control. These are configured in `/etc/asound.conf` or `~/.asoundrc`.

**Common ALSA debugging commands**:

```bash
# List all sound cards
aplay -l

# List all PCM playback devices
aplay -L

# Test audio output (white noise)
speaker-test -D hw:sndrpihifiberry -c 2

# Show all mixer controls
amixer -c 0 contents

# Interactive mixer (ncurses UI)
alsamixer

# Record from an input for testing
arecord -D hw:1 -f S16_LE -r 48000 -c 2 -d 5 test.wav
```

**Why multiple sources sometimes conflict**: By default, ALSA allows only one application to access a hardware device at a time. If MPD has the DAC open and shairport-sync tries to play, it fails. Solutions:

1. **dmix plugin** (default on most distributions): Mixes multiple streams in software before sending to hardware. Works but adds slight latency.
2. **ALSA loopback + CamillaDSP**: All sources output to the loopback device (which accepts unlimited streams via dmix), and CamillaDSP reads from the loopback and outputs to the real DAC. This is the approach we use in Chapters 29-30.
3. **PulseAudio/PipeWire**: Higher-level audio servers that handle routing automatically. moOde doesn't use these (it uses ALSA directly for minimum latency), but they're an option on standard Raspberry Pi OS.

### 9.4 Bluetooth Streaming: The Backup Option

We haven't discussed Bluetooth much because it's not ideal for a stationary streamer (wired and WiFi connections are better in every way). But moOde supports Bluetooth audio reception out of the box, which means any phone can pair with the Pi and play audio over Bluetooth.

**Bluetooth audio codecs**:

| Codec | Bitrate | Quality | Latency | Notes |
|---|---|---|---|---|
| SBC | 328 kbps | Adequate | 100-200 ms | Mandatory baseline, every BT device supports it |
| AAC | 256 kbps | Good | 100-200 ms | Apple's preferred BT codec, good quality |
| aptX | 352 kbps | Good | 70 ms | Qualcomm codec, widely supported on Android |
| aptX HD | 576 kbps | Very good | 100 ms | 24-bit aptX, less common |
| LDAC | Up to 990 kbps | Excellent | 100-200 ms | Sony codec, near-lossless at highest bitrate |
| LC3 | Variable | Very good | 60 ms | Bluetooth LE Audio codec, new standard |

The Pi 4's built-in Bluetooth supports SBC and AAC. For aptX or LDAC reception, you'd need a USB Bluetooth adapter that supports those codecs (like the Creative BT-W5). moOde handles the Bluetooth audio reception through BlueZ and routes it to ALSA.

**When Bluetooth is useful**: When a guest wants to play music from their phone and isn't on your WiFi network. It's the universal fallback.

### 9.5 MPD: The Audio Engine Under the Hood

Both moOde and Volumio use **MPD (Music Player Daemon)** as their core audio engine. MPD is a server-side application for playing music -- it manages a database of your music files, handles playlists, decodes audio formats, and outputs the audio stream to ALSA.

You control MPD through its protocol (a simple text-based TCP protocol on port 6600). The web UI, your physical control scripts, and command-line tools all communicate with MPD through this protocol.

**Useful MPD commands** (via `mpc`, the MPD command-line client):

```bash
# Install mpc (command-line MPD client)
sudo apt install mpc

# Basic controls
mpc play
mpc pause
mpc next
mpc prev
mpc volume 75      # Set volume to 75%
mpc status         # Show current track and state

# Library management
mpc update          # Scan for new files
mpc listall         # List all tracks
mpc search artist "Miles Davis"   # Search

# Queue management
mpc add ""          # Add entire library to queue
mpc shuffle         # Shuffle the queue
mpc clear           # Clear the queue

# Output control
mpc outputs         # List audio outputs
mpc enable 1        # Enable output #1
mpc disable 2       # Disable output #2
```

Understanding MPD's protocol is valuable because your Python control scripts (Chapter 28) communicate with MPD through the `python-mpd2` library, which is a thin wrapper around this protocol. When debugging transport control issues, `mpc` is your best friend.

### 9.6 What's Next

You now understand the protocols that deliver audio to your Pi and the software that manages playback. Before we build it (Chapter 28), we need one more piece of theory: DSP. Chapter 27 covers the digital signal processing concepts that will turn your Pi from a simple streamer into an active crossover and room correction engine.

---

## Summary

Network audio follows a source-transport-renderer architecture. Your Pi is the renderer. Audio reaches it via DLNA/UPnP (local library), AirPlay 2 (Apple ecosystem), Spotify Connect (direct from Spotify's CDN), or Roon/RAAT (premium library management). Each protocol handles discovery, session management, and data delivery differently, but they all end at the same place: PCM samples being sent to your DAC.

For the Pi, moOde Audio is the recommended distribution -- it's fully open source, integrates CamillaDSP for the DSP builds in later chapters, and gives you full control over GPIO for physical controls. Volumio is the easier alternative if you prefer a more polished UI.

Multi-room audio is achievable through Snapcast (open source, MPD-based), AirPlay 2 (Apple ecosystem), or Roon (premium). Snapcast provides sub-millisecond sync over Ethernet and works well with moOde.

Use Ethernet for your streamer. WiFi works but introduces reliability risks that Ethernet eliminates. Store your library on a NAS in FLAC format with clean metadata. And don't overthink hi-res -- the mastering quality matters far more than the sample rate.
