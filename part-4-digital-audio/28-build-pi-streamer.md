<!--
  CHAPTER: 28
  TITLE: "Build: Pi Streamer with Physical Controls"
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 27
  KEY_TOPICS: Raspberry Pi, DAC HAT, HiFiBerry, I2S, Volumio, moOde, rotary encoder, GPIO, OLED display, physical controls, systemd, volume control, transport buttons, debouncing
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 28: Build — Pi Streamer with Physical Controls

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 27 | Difficulty: Intermediate

This is the build chapter. Theory is over. We're going to assemble a Raspberry Pi-based music streamer that sounds genuinely excellent and has physical controls that work whether the app is open, crashed, or your WiFi is down. Volume knob. Transport buttons. An OLED display showing what's playing. All of it running as independent services that start on boot and don't care about anything else on the system.

This is a core design principle, not an afterthought: **the physical controls are first-class citizens.** If the Pi's web interface crashes, you can still adjust volume and skip tracks. If your phone dies, the music keeps playing and the controls keep working. If the network goes down, local playback from a USB drive still works with full control. The system degrades gracefully because the controls are tied to the OS-level audio subsystem, not to any single application.

By the end of this chapter, you'll have a working streamer that plays Spotify, AirPlay, and your local library, with a volume knob that feels as immediate and satisfying as the one on the vintage amplifier you might be connecting it to.

### In This Chapter
- Hardware selection: Pi model, DAC HAT, controls, display
- Software installation: moOde Audio setup and configuration
- Enabling Spotify Connect, AirPlay, and DLNA
- Physical controls: rotary encoder for volume (full code)
- Transport buttons: play/pause, skip, previous (full code)
- OLED display: track info, volume, album art (full code)
- Running control scripts as systemd services
- Case design and assembly
- Wiring diagram
- First test and troubleshooting

### Related Chapters
- [Ch 25: Digital Audio Fundamentals](25-digital-audio-fundamentals.md) -- why we chose this DAC architecture
- [Ch 26: Streaming Architectures and Protocols](26-streaming-architectures-protocols.md) -- the software stack
- [Ch 27: DSP Fundamentals for Audio](27-dsp-fundamentals.md) -- the processing this Pi will do in Chapter 29
- [Ch 23: Build: Chip Amp and Class D](../part-3-amplifiers/23-chip-amp-class-d.md) -- the amplifier this streamer drives
- [Ch 29: Build: Active Crossover and Room Correction](29-build-active-crossover-room-correction.md) -- adding DSP to this build
- [Ch 32: Enclosure Design](../part-5-pcb-professional/32-enclosure-design.md) -- case design principles

---

## 1. Design Philosophy

### 1.1 Why Physical Controls Matter

Here's the scenario: you're hosting a dinner party. Music is playing from your Pi streamer. A song comes on that doesn't fit the mood. You need to skip it. Now:

**App-dependent system**: Pull out your phone. Unlock it. Find the Spotify app. Wait for it to connect. Press skip. Total time: 8-15 seconds, plus you've broken conversation to stare at your phone.

**Physical controls**: Reach over. Press the skip button. Done. Sub-second. You never broke eye contact with your guest.

Or: music is too loud. With an app, you're fumbling for your phone. With a knob, you reach over and turn it down. Immediate. Tactile. Zero latency.

Physical controls aren't a retro affectation. They're a superior user interface for a single-purpose device. Your phone is a general-purpose computer that happens to have a music remote function buried somewhere in its UI. A dedicated volume knob is a purpose-built interface with zero cognitive overhead.

### 1.2 Design Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Pi Model | Raspberry Pi 4 (4GB) or Pi 5 | Pi 4 is proven and widely supported; Pi 5 is faster (helpful for DSP in Ch 29). Either works. |
| DAC | HiFiBerry DAC2 Pro | Excellent measured performance (112 dB SNR), PCM5142 chip, direct I2S, hardware volume control, ~$45. |
| Volume Control | KY-040 rotary encoder with push button | Infinite rotation, tactile detents, built-in push-to-mute. |
| Transport Buttons | 4x tactile push buttons | Play/pause, next, previous, and a function button. |
| Display | SSD1306 128x64 OLED (I2C) | Crisp, wide viewing angle, low power, $3-5, dead simple over I2C. |
| OS | moOde Audio | Open source, CamillaDSP integration, GPIO-friendly. |

---

## 2. Hardware: Bill of Materials

### 2.1 Core Components

| Component | Specific Part | Approx. Cost | Notes |
|---|---|---|---|
| Raspberry Pi 4 Model B (4GB) | — | $55 | 2GB works too, but 4GB gives headroom for DSP |
| MicroSD card (32GB+) | Samsung EVO Plus or SanDisk Extreme | $8 | Class 10 / A1 minimum |
| HiFiBerry DAC2 Pro | — | $45 | Mounts directly on GPIO header |
| Power supply | Official Pi USB-C, 5V 3A | $10 | Use the official one -- cheap supplies cause audio noise |
| Ethernet cable | Cat 5e or better | $5 | If using Ethernet (recommended) |

### 2.2 Physical Control Components

| Component | Specific Part | Approx. Cost | Notes |
|---|---|---|---|
| Rotary encoder | KY-040 (with breakout board) | $2 | 20 detents per rotation, built-in push button |
| Tactile push buttons (x4) | 6mm through-hole, panel mount | $4 (pack) | Get ones with caps for a finished look |
| OLED display | SSD1306, 128x64, I2C, 0.96" | $4 | White or blue; I2C address 0x3C |
| Hookup wire | 22 AWG solid core, assorted colors | $8 | Or DuPont jumper wires for prototyping |
| Pin headers / DuPont connectors | — | $3 | For clean connections |

### 2.3 Optional Extras

| Component | Purpose | Approx. Cost |
|---|---|---|
| 40-pin GPIO stacking header | Access GPIO pins above the DAC HAT | $3 |
| 3D-printed or acrylic case | Enclosure | $10-30 (materials) |
| Standoffs, M2.5 screws | Mounting | $5 |
| LED (x2) | Power indicator, network activity | $1 |

**Total cost**: ~$145 for everything. Less than most commercial streamers, and infinitely more capable.

### 2.4 DAC HAT Comparison

If the HiFiBerry DAC2 Pro isn't available or you want to compare:

| DAC HAT | Chip | SNR | Price | Notes |
|---|---|---|---|---|
| HiFiBerry DAC2 Pro | PCM5142 | 112 dB | $45 | Best balance of price/performance. Dedicated clock. |
| HiFiBerry DAC2 HD | PCM1796 | 114 dB | $65 | Higher-end chip, balanced output option. |
| IQaudIO DAC Pro | PCM5122 | 112 dB | $40 | Similar to HiFiBerry, good quality. |
| JustBoom DAC HAT | PCM5122 | 112 dB | $35 | Budget option, still very good. |
| Boss DAC v1.2 | PCM5122 | 112 dB | $40 | Includes S/PDIF output. |

All of these connect via I2S and are supported by moOde Audio. The PCM5122/PCM5142 family are the most common DAC chips in Pi HATs, and they're genuinely excellent. The performance differences between these boards are measurable but not audible in any real listening scenario.

> **What happens if...** you use a cheap USB DAC instead of an I2S HAT? It will work -- Linux has excellent USB audio support. But you lose the advantage of I2S (lower jitter from direct connection, cleaner power from the HAT's dedicated regulators) and add the complexity of USB audio configuration. Unless you need a specific feature of a USB DAC (like multi-channel output for the active crossover in Chapter 29), stick with a HAT.

---

## 3. Hardware Assembly

### 3.1 GPIO Pin Usage

The HiFiBerry DAC2 Pro uses specific GPIO pins for I2S:

| GPIO Pin | Function | Used By |
|---|---|---|
| GPIO 18 (pin 12) | I2S BCLK (bit clock) | DAC HAT |
| GPIO 19 (pin 35) | I2S LRCLK (L/R clock) | DAC HAT |
| GPIO 20 (pin 38) | I2S DIN (data to DAC) | DAC HAT |
| GPIO 21 (pin 40) | I2S DOUT (data from DAC) | DAC HAT |
| GPIO 2 (pin 3) | I2C SDA | OLED display (shared with DAC HAT if used) |
| GPIO 3 (pin 5) | I2C SCL | OLED display (shared with DAC HAT if used) |

The remaining GPIO pins are available for our controls. Here's the pin assignment:

| GPIO Pin | Physical Pin | Function |
|---|---|---|
| GPIO 17 | Pin 11 | Rotary encoder CLK (A) |
| GPIO 27 | Pin 13 | Rotary encoder DT (B) |
| GPIO 22 | Pin 15 | Rotary encoder push button (SW) |
| GPIO 5 | Pin 29 | Button: Play/Pause |
| GPIO 6 | Pin 31 | Button: Next Track |
| GPIO 13 | Pin 33 | Button: Previous Track |
| GPIO 26 | Pin 37 | Button: Function (mute/source select) |
| GPIO 2 | Pin 3 | I2C SDA (OLED display) |
| GPIO 3 | Pin 5 | I2C SCL (OLED display) |
| 3.3V | Pin 1 | Power for encoder + buttons |
| GND | Pin 6, 9, 14, etc. | Ground for all components |

### 3.2 Stacking Header

The HiFiBerry DAC2 Pro sits on top of the Pi's GPIO header. To access the GPIO pins for our controls, you need a **stacking header** -- a female header that passes through the DAC HAT's header and extends above it, giving you access to all 40 pins.

Install the stacking header *before* mounting the DAC HAT:
1. Remove the standard female header from the DAC HAT (if it came with one)
2. Solder the stacking header in its place
3. Mount the DAC HAT on the Pi -- the stacking header's extended pins are now accessible above the HAT

### 3.3 Wiring the Rotary Encoder

The KY-040 encoder has 5 pins:

```
KY-040 Pin    →    Pi GPIO
────────────────────────────
CLK (A)       →    GPIO 17 (pin 11)
DT  (B)       →    GPIO 27 (pin 13)
SW  (push)    →    GPIO 22 (pin 15)
+             →    3.3V (pin 1)
GND           →    GND (pin 6)
```

The encoder's CLK and DT pins produce quadrature signals when rotated -- two square waves 90 degrees out of phase. The direction of rotation determines which signal leads. Our Python code will decode this.

The push button (SW) connects to ground when pressed. We'll enable the Pi's internal pull-up resistor on GPIO 22 so the pin reads HIGH when unpressed and LOW when pressed.

### 3.4 Wiring the Transport Buttons

Each button connects between its GPIO pin and ground. We use the Pi's internal pull-up resistors so each pin reads HIGH normally and LOW when the button is pressed.

```
Button          →    Pi GPIO         →    Other leg to GND
──────────────────────────────────────────────────────────
Play/Pause      →    GPIO 5 (pin 29) →    GND (pin 30)
Next Track      →    GPIO 6 (pin 31) →    GND (pin 34)
Previous Track  →    GPIO 13 (pin 33)→    GND (pin 34)
Function        →    GPIO 26 (pin 37)→    GND (pin 39)
```

### 3.5 Wiring the OLED Display

The SSD1306 OLED connects via I2C:

```
OLED Pin    →    Pi GPIO
──────────────────────────
VCC         →    3.3V (pin 1)
GND         →    GND (pin 9)
SDA         →    GPIO 2 / SDA1 (pin 3)
SCL         →    GPIO 3 / SCL1 (pin 5)
```

I2C is a shared bus -- the OLED display and the DAC HAT's I2C control interface can coexist on the same SDA/SCL lines (they have different I2C addresses). The SSD1306 is typically at address 0x3C.

### 3.6 Complete Wiring Diagram

```
                    Raspberry Pi 4 GPIO Header (top view)
                    ┌─────────────────────────────────┐
             3.3V  │ [1]  [2]  │ 5V
     OLED SDA/I2C  │ [3]  [4]  │ 5V
     OLED SCL/I2C  │ [5]  [6]  │ GND ← Encoder GND, Button GND
                   │ [7]  [8]  │
                   │ [9]  [10] │
    Encoder CLK ─→ │ [11] [12] │ ← I2S BCLK (DAC HAT)
    Encoder DT  ─→ │ [13] [14] │ GND
    Encoder SW  ─→ │ [15] [16] │
                   │ [17] [18] │
                   │ [19] [20] │
                   │ [21] [22] │
                   │ [23] [24] │
                   │ [25] [26] │
                   │ [27] [28] │
    Play/Pause  ─→ │ [29] [30] │ GND ← Button GND
    Next Track  ─→ │ [31] [32] │
    Prev Track  ─→ │ [33] [34] │ GND ← Button GND
                   │ [35] [36] │ ← I2S LRCLK (DAC HAT)
    Function    ─→ │ [37] [38] │ ← I2S DIN (DAC HAT)
                   │ [39] [40] │ ← I2S DOUT (DAC HAT)
                    └─────────────────────────────────┘
```

---

## 4. Software Installation

### 4.1 Installing moOde Audio

1. Download the latest moOde Audio image from [moodeaudio.org](https://moodeaudio.org)
2. Write it to the MicroSD card:

```bash
# On your computer (macOS/Linux)
# Find your SD card device (BE CAREFUL -- wrong device = wiped hard drive)
diskutil list                    # macOS
lsblk                          # Linux

# Write the image
sudo dd if=moode-audio-x.x.x.img of=/dev/rdiskN bs=4m status=progress
# Or use Raspberry Pi Imager (GUI tool) -- it's safer and easier
```

3. Insert the SD card into the Pi
4. Connect Ethernet (recommended) or prepare for WiFi setup
5. Power on the Pi

### 4.2 First Boot Configuration

On first boot, moOde creates a WiFi hotspot called "Moode" if no Ethernet is connected. Connect to it and navigate to `http://moode.local` or `http://172.24.1.1`.

With Ethernet connected, navigate to `http://moode.local` from any computer on the same network.

**Configure the DAC HAT**:
1. Go to **System Config** (gear icon) → **Audio**
2. Under "Named I2S device," select **HiFiBerry DAC2 Pro** (or your specific HAT)
3. Click **SET** and reboot when prompted
4. After reboot, go to **Audio Config** and verify the I2S device is active

**Verify audio output**:
1. Go to the moOde library
2. Add a test file (or connect a USB drive with music)
3. Press play -- you should hear audio from the DAC HAT's RCA outputs

### 4.3 Enable Spotify Connect

In moOde's web interface:
1. Go to **System Config** → **Renderers**
2. Enable **Spotify Connect** (uses librespot)
3. Set the device name (e.g., "Pi Streamer")
4. Set bitrate to 320 kbps
5. Click **SAVE**

Open Spotify on your phone. You should see "Pi Streamer" as an available device in the Spotify Connect menu.

### 4.4 Enable AirPlay

1. Go to **System Config** → **Renderers**
2. Enable **AirPlay** (uses shairport-sync)
3. Set the device name
4. Click **SAVE**

Your Pi will appear as an AirPlay speaker on any Apple device on the network.

### 4.5 Enable I2C for the OLED Display

```bash
# SSH into the Pi (default: pi@moode.local, password: moodeaudio)
ssh pi@moode.local

# Enable I2C
sudo raspi-config
# Navigate to: Interface Options → I2C → Enable
# Or directly:
sudo raspi-config nonint do_i2c 0

# Verify I2C is working and the OLED is detected
sudo apt install -y i2c-tools
i2cdetect -y 1
# You should see "3c" in the output (the SSD1306's address)
```

---

## 5. Physical Controls: The Code

### 5.1 Dependencies

```bash
# SSH into the Pi
ssh pi@moode.local

# Install Python dependencies
sudo apt install -y python3-pip python3-dev python3-pil python3-smbus
pip3 install RPi.GPIO luma.oled python-mpd2 requests
```

**What each library does**:
- `RPi.GPIO`: Reads GPIO pins (encoder, buttons)
- `luma.oled`: Drives the SSD1306 OLED display
- `python-mpd2`: Communicates with MPD (moOde's audio engine) for track info and playback control
- `Pillow` (python3-pil): Image rendering for the display
- `requests`: HTTP calls to moOde's API

### 5.2 Volume Control: Rotary Encoder Script

This is the most important control. It must be responsive, accurate, and independent.

```python
#!/usr/bin/env python3
"""
Pi Streamer Volume Control — Rotary Encoder
Reads a KY-040 rotary encoder and adjusts ALSA volume.
Runs as a standalone systemd service.
"""

import RPi.GPIO as GPIO
import subprocess
import time
import threading

# --- Configuration ---
ENCODER_CLK = 17   # GPIO pin for encoder CLK (A)
ENCODER_DT = 27    # GPIO pin for encoder DT (B)
ENCODER_SW = 22    # GPIO pin for encoder push button
VOLUME_STEP = 2    # Volume change per detent (percentage points)
ALSA_MIXER = "Digital"  # ALSA mixer name for HiFiBerry DAC2 Pro
# Use "Master" or "PCM" for other DACs -- run `amixer scontrols` to find yours

# --- State ---
current_volume = 50
muted = False
pre_mute_volume = 50
lock = threading.Lock()


def get_current_volume():
    """Read the current ALSA mixer volume."""
    try:
        result = subprocess.run(
            ["amixer", "sget", ALSA_MIXER],
            capture_output=True, text=True, timeout=2
        )
        for line in result.stdout.split("\n"):
            if "%" in line:
                # Extract percentage from e.g., "[75%]"
                start = line.index("[") + 1
                end = line.index("%")
                return int(line[start:end])
    except Exception:
        pass
    return 50  # Default fallback


def set_volume(vol):
    """Set the ALSA mixer volume (0-100)."""
    vol = max(0, min(100, vol))
    subprocess.run(
        ["amixer", "sset", ALSA_MIXER, f"{vol}%"],
        capture_output=True, timeout=2
    )
    return vol


def encoder_callback(channel):
    """Called on encoder CLK edge. Determines direction and adjusts volume."""
    global current_volume, muted

    # Read both pins to determine direction
    clk_state = GPIO.input(ENCODER_CLK)
    dt_state = GPIO.input(ENCODER_DT)

    with lock:
        if muted:
            # Any rotation unmutes
            muted = False

        if clk_state != dt_state:
            # Clockwise -- volume up
            current_volume = min(100, current_volume + VOLUME_STEP)
        else:
            # Counter-clockwise -- volume down
            current_volume = max(0, current_volume - VOLUME_STEP)

        set_volume(current_volume)


def button_callback(channel):
    """Called on encoder push button press. Toggles mute."""
    global muted, current_volume, pre_mute_volume

    # Debounce: ignore if called within 300ms of last press
    time.sleep(0.05)  # Wait for bounce to settle
    if GPIO.input(ENCODER_SW) != 0:
        return  # False trigger

    with lock:
        if muted:
            # Unmute: restore previous volume
            muted = False
            current_volume = pre_mute_volume
            set_volume(current_volume)
        else:
            # Mute: save current volume, set to 0
            muted = True
            pre_mute_volume = current_volume
            current_volume = 0
            set_volume(0)


def main():
    global current_volume

    # Setup GPIO
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(ENCODER_CLK, GPIO.IN, pull_up_down=GPIO.PUD_UP)
    GPIO.setup(ENCODER_DT, GPIO.IN, pull_up_down=GPIO.PUD_UP)
    GPIO.setup(ENCODER_SW, GPIO.IN, pull_up_down=GPIO.PUD_UP)

    # Read initial volume from ALSA
    current_volume = get_current_volume()
    print(f"Volume control started. Current volume: {current_volume}%")

    # Register interrupt callbacks
    GPIO.add_event_detect(
        ENCODER_CLK, GPIO.FALLING,
        callback=encoder_callback,
        bouncetime=5
    )
    GPIO.add_event_detect(
        ENCODER_SW, GPIO.FALLING,
        callback=button_callback,
        bouncetime=300
    )

    # Keep the script running
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        pass
    finally:
        GPIO.cleanup()


if __name__ == "__main__":
    main()
```

**Key design decisions**:
- Volume is controlled via **ALSA mixer** (`amixer`), not through MPD or any player application. This means it works regardless of which source is playing (Spotify, AirPlay, local files, anything that outputs to ALSA).
- The encoder uses **interrupt-driven** callbacks (not polling), so CPU usage is essentially zero when the knob isn't being turned.
- **Thread safety**: A lock protects the volume state from race conditions between rotation and button callbacks.
- **Debouncing**: The `bouncetime` parameter in `add_event_detect` prevents multiple triggers from mechanical bounce. The push button has additional software debounce (50ms delay + re-read).

### 5.3 Transport Buttons Script

```python
#!/usr/bin/env python3
"""
Pi Streamer Transport Controls — Push Buttons
Reads GPIO buttons and sends commands to MPD.
Runs as a standalone systemd service.
"""

import RPi.GPIO as GPIO
import time
from mpd import MPDClient

# --- Configuration ---
BTN_PLAY_PAUSE = 5    # GPIO pin
BTN_NEXT = 6          # GPIO pin
BTN_PREV = 13         # GPIO pin
BTN_FUNC = 26         # GPIO pin
MPD_HOST = "localhost"
MPD_PORT = 6600
DEBOUNCE_MS = 250     # Debounce time in milliseconds


def get_mpd_client():
    """Create and return a connected MPD client."""
    client = MPDClient()
    client.timeout = 5
    try:
        client.connect(MPD_HOST, MPD_PORT)
        return client
    except Exception as e:
        print(f"MPD connection failed: {e}")
        return None


def mpd_command(func_name, *args):
    """Execute an MPD command with automatic reconnection."""
    client = get_mpd_client()
    if client is None:
        return
    try:
        func = getattr(client, func_name)
        func(*args)
    except Exception as e:
        print(f"MPD command '{func_name}' failed: {e}")
    finally:
        try:
            client.close()
            client.disconnect()
        except Exception:
            pass


def on_play_pause(channel):
    """Toggle play/pause."""
    time.sleep(0.05)
    if GPIO.input(BTN_PLAY_PAUSE) != 0:
        return
    print("Button: Play/Pause")
    client = get_mpd_client()
    if client:
        try:
            status = client.status()
            if status.get("state") == "play":
                client.pause(1)
            else:
                client.play()
        except Exception as e:
            print(f"Play/pause failed: {e}")
        finally:
            try:
                client.close()
                client.disconnect()
            except Exception:
                pass


def on_next(channel):
    """Skip to next track."""
    time.sleep(0.05)
    if GPIO.input(BTN_NEXT) != 0:
        return
    print("Button: Next track")
    mpd_command("next")


def on_prev(channel):
    """Go to previous track."""
    time.sleep(0.05)
    if GPIO.input(BTN_PREV) != 0:
        return
    print("Button: Previous track")
    mpd_command("previous")


def on_function(channel):
    """Function button -- toggle repeat mode (customize as needed)."""
    time.sleep(0.05)
    if GPIO.input(BTN_FUNC) != 0:
        return
    print("Button: Function")
    client = get_mpd_client()
    if client:
        try:
            status = client.status()
            repeat = int(status.get("repeat", 0))
            client.repeat(1 - repeat)  # Toggle
        except Exception as e:
            print(f"Function failed: {e}")
        finally:
            try:
                client.close()
                client.disconnect()
            except Exception:
                pass


def main():
    GPIO.setmode(GPIO.BCM)

    # Setup all button pins with pull-up resistors
    for pin in [BTN_PLAY_PAUSE, BTN_NEXT, BTN_PREV, BTN_FUNC]:
        GPIO.setup(pin, GPIO.IN, pull_up_down=GPIO.PUD_UP)

    # Register callbacks
    GPIO.add_event_detect(BTN_PLAY_PAUSE, GPIO.FALLING,
                          callback=on_play_pause, bouncetime=DEBOUNCE_MS)
    GPIO.add_event_detect(BTN_NEXT, GPIO.FALLING,
                          callback=on_next, bouncetime=DEBOUNCE_MS)
    GPIO.add_event_detect(BTN_PREV, GPIO.FALLING,
                          callback=on_prev, bouncetime=DEBOUNCE_MS)
    GPIO.add_event_detect(BTN_FUNC, GPIO.FALLING,
                          callback=on_function, bouncetime=DEBOUNCE_MS)

    print("Transport controls started.")

    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        pass
    finally:
        GPIO.cleanup()


if __name__ == "__main__":
    main()
```

**Design note on Spotify Connect**: When streaming via Spotify Connect (librespot), transport commands go through MPD if moOde is configured to route Spotify through MPD. If not, you may need to also send commands via Spotify's local API. moOde's architecture handles this -- the transport buttons will work for local playback and Spotify Connect. For AirPlay, transport control is more limited (shairport-sync supports metadata but transport control depends on the source device).

### 5.4 OLED Display Script

```python
#!/usr/bin/env python3
"""
Pi Streamer OLED Display — SSD1306 128x64
Shows current track, artist, volume level, and playback state.
Runs as a standalone systemd service.
"""

import time
import subprocess
import threading
from PIL import Image, ImageDraw, ImageFont
from luma.core.interface.serial import i2c
from luma.oled.device import ssd1306
from mpd import MPDClient

# --- Configuration ---
I2C_PORT = 1
I2C_ADDRESS = 0x3C
DISPLAY_WIDTH = 128
DISPLAY_HEIGHT = 64
MPD_HOST = "localhost"
MPD_PORT = 6600
UPDATE_INTERVAL = 0.5  # seconds between display updates
SCROLL_SPEED = 2       # pixels per update for scrolling text
ALSA_MIXER = "Digital"

# --- Display Setup ---
serial = i2c(port=I2C_PORT, address=I2C_ADDRESS)
device = ssd1306(serial, width=DISPLAY_WIDTH, height=DISPLAY_HEIGHT)

# Font setup -- use a monospace font if available, otherwise default
try:
    font_large = ImageFont.truetype("/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf", 14)
    font_small = ImageFont.truetype("/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 10)
    font_tiny = ImageFont.truetype("/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 8)
except Exception:
    font_large = ImageFont.load_default()
    font_small = ImageFont.load_default()
    font_tiny = ImageFont.load_default()


class DisplayState:
    """Holds the current display state."""
    def __init__(self):
        self.title = ""
        self.artist = ""
        self.album = ""
        self.state = "stop"  # play, pause, stop
        self.volume = 50
        self.source = "MPD"
        self.elapsed = 0
        self.duration = 0
        self.title_scroll_offset = 0
        self.artist_scroll_offset = 0


state = DisplayState()


def get_volume():
    """Read current ALSA volume."""
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


def update_mpd_state():
    """Poll MPD for current track and playback info."""
    client = MPDClient()
    client.timeout = 3
    try:
        client.connect(MPD_HOST, MPD_PORT)
        status = client.status()
        song = client.currentsong()

        state.state = status.get("state", "stop")
        state.volume = get_volume()
        state.elapsed = float(status.get("elapsed", 0))
        state.duration = float(status.get("duration", 0))

        new_title = song.get("title", song.get("file", "No Track"))
        new_artist = song.get("artist", "Unknown Artist")
        state.album = song.get("album", "")

        # Reset scroll if track changed
        if new_title != state.title:
            state.title_scroll_offset = 0
        if new_artist != state.artist:
            state.artist_scroll_offset = 0

        state.title = new_title
        state.artist = new_artist

        client.close()
        client.disconnect()
    except Exception:
        state.title = "No Connection"
        state.artist = ""
        state.state = "stop"


def draw_scrolling_text(draw, y, text, font, max_width):
    """Draw text that scrolls horizontally if too wide."""
    text_bbox = draw.textbbox((0, 0), text, font=font)
    text_width = text_bbox[2] - text_bbox[0]

    if text_width <= max_width:
        # Text fits -- draw it centered or left-aligned
        draw.text((0, y), text, font=font, fill="white")
        return 0  # No scrolling needed
    else:
        # Text is too wide -- scroll it
        # Add padding for wrap-around
        padded_text = text + "     " + text
        return text_width + 40  # Return total scroll width


def render_display():
    """Render the current state to the OLED display."""
    image = Image.new("1", (DISPLAY_WIDTH, DISPLAY_HEIGHT), "black")
    draw = ImageDraw.Draw(image)

    if state.state == "stop" and state.title == "No Connection":
        # Idle / no connection screen
        draw.text((10, 20), "Pi Streamer", font=font_large, fill="white")
        draw.text((20, 40), "Ready", font=font_small, fill="white")
    else:
        # --- Now Playing Screen ---

        # Line 1: Title (scrolling if needed, y=0)
        title_bbox = draw.textbbox((0, 0), state.title, font=font_large)
        title_width = title_bbox[2] - title_bbox[0]
        if title_width > DISPLAY_WIDTH:
            # Scroll the title
            x_offset = -(state.title_scroll_offset % (title_width + 60))
            draw.text((x_offset, 0), state.title, font=font_large, fill="white")
            draw.text((x_offset + title_width + 60, 0), state.title,
                       font=font_large, fill="white")
            state.title_scroll_offset += SCROLL_SPEED
        else:
            draw.text((0, 0), state.title, font=font_large, fill="white")

        # Line 2: Artist (y=18)
        artist_bbox = draw.textbbox((0, 0), state.artist, font=font_small)
        artist_width = artist_bbox[2] - artist_bbox[0]
        if artist_width > DISPLAY_WIDTH:
            x_offset = -(state.artist_scroll_offset % (artist_width + 60))
            draw.text((x_offset, 18), state.artist, font=font_small, fill="white")
            draw.text((x_offset + artist_width + 60, 18), state.artist,
                       font=font_small, fill="white")
            state.artist_scroll_offset += SCROLL_SPEED
        else:
            draw.text((0, 18), state.artist, font=font_small, fill="white")

        # Line 3: Separator
        draw.line([(0, 32), (DISPLAY_WIDTH, 32)], fill="white")

        # Line 4: Play state icon + Progress bar (y=36)
        if state.state == "play":
            icon = "\u25B6"  # Play triangle
        elif state.state == "pause":
            icon = "||"
        else:
            icon = "\u25A0"  # Stop square

        draw.text((0, 35), icon, font=font_small, fill="white")

        # Progress bar
        bar_x = 20
        bar_y = 38
        bar_width = 90
        bar_height = 6
        draw.rectangle([(bar_x, bar_y), (bar_x + bar_width, bar_y + bar_height)],
                        outline="white")
        if state.duration > 0:
            progress = state.elapsed / state.duration
            fill_width = int(bar_width * progress)
            draw.rectangle([(bar_x, bar_y),
                           (bar_x + fill_width, bar_y + bar_height)],
                           fill="white")

        # Time elapsed
        elapsed_min = int(state.elapsed) // 60
        elapsed_sec = int(state.elapsed) % 60
        draw.text((112, 35), f"{elapsed_min}:{elapsed_sec:02d}",
                  font=font_tiny, fill="white")

        # Line 5: Volume bar (y=50)
        draw.text((0, 50), "Vol", font=font_tiny, fill="white")
        vol_bar_x = 22
        vol_bar_y = 52
        vol_bar_width = 80
        vol_bar_height = 6
        draw.rectangle([(vol_bar_x, vol_bar_y),
                        (vol_bar_x + vol_bar_width, vol_bar_y + vol_bar_height)],
                        outline="white")
        vol_fill = int(vol_bar_width * state.volume / 100)
        draw.rectangle([(vol_bar_x, vol_bar_y),
                        (vol_bar_x + vol_fill, vol_bar_y + vol_bar_height)],
                        fill="white")
        draw.text((105, 50), f"{state.volume}%", font=font_tiny, fill="white")

    device.display(image)


def main():
    print("OLED display service started.")
    device.contrast(200)  # Set brightness (0-255)

    while True:
        try:
            update_mpd_state()
            render_display()
            time.sleep(UPDATE_INTERVAL)
        except KeyboardInterrupt:
            break
        except Exception as e:
            print(f"Display error: {e}")
            time.sleep(1)

    device.hide()


if __name__ == "__main__":
    main()
```

**Display layout** (128x64 pixels):

```
┌────────────────────────────────┐
│ Song Title (scrolling)         │  <- 14px font, bold
│ Artist Name (scrolling)        │  <- 10px font
│────────────────────────────────│
│ ▶ [████████░░░░░] 3:42         │  <- play state + progress
│ Vol [██████████░░] 72%         │  <- volume bar
└────────────────────────────────┘
```

---

## 6. Systemd Services: Making It All Automatic

### 6.1 Why Systemd

Every control script must:
- Start automatically on boot
- Restart if it crashes
- Run independently of each other and of the music player
- Run as a background service with no terminal required

Systemd handles all of this.

### 6.2 Install the Scripts

```bash
# Create a directory for the control scripts
sudo mkdir -p /opt/pistreamer
sudo cp volume_control.py /opt/pistreamer/
sudo cp transport_buttons.py /opt/pistreamer/
sudo cp oled_display.py /opt/pistreamer/
sudo chmod +x /opt/pistreamer/*.py
```

### 6.3 Volume Control Service

```bash
sudo tee /etc/systemd/system/pistreamer-volume.service << 'EOF'
[Unit]
Description=Pi Streamer Volume Control (Rotary Encoder)
After=sound.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/pistreamer/volume_control.py
Restart=on-failure
RestartSec=3
User=root
Nice=-10

[Install]
WantedBy=multi-user.target
EOF
```

Note: `User=root` is needed for GPIO access. `Nice=-10` gives the volume control higher scheduling priority so it responds instantly even if the CPU is busy with DSP processing.

### 6.4 Transport Buttons Service

```bash
sudo tee /etc/systemd/system/pistreamer-transport.service << 'EOF'
[Unit]
Description=Pi Streamer Transport Controls (Buttons)
After=mpd.service

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/pistreamer/transport_buttons.py
Restart=on-failure
RestartSec=3
User=root

[Install]
WantedBy=multi-user.target
EOF
```

### 6.5 OLED Display Service

```bash
sudo tee /etc/systemd/system/pistreamer-display.service << 'EOF'
[Unit]
Description=Pi Streamer OLED Display
After=mpd.service

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/pistreamer/oled_display.py
Restart=on-failure
RestartSec=3
User=root

[Install]
WantedBy=multi-user.target
EOF
```

### 6.6 Enable and Start All Services

```bash
sudo systemctl daemon-reload
sudo systemctl enable pistreamer-volume pistreamer-transport pistreamer-display
sudo systemctl start pistreamer-volume pistreamer-transport pistreamer-display

# Verify they're running
sudo systemctl status pistreamer-volume
sudo systemctl status pistreamer-transport
sudo systemctl status pistreamer-display
```

### 6.7 Checking Logs

```bash
# View logs for any service
journalctl -u pistreamer-volume -f   # Follow volume control logs
journalctl -u pistreamer-display -f  # Follow display logs
```

---

## 7. Case Design

### 7.1 Design Principles

The case needs to:
- Accommodate the Pi + DAC HAT stack (~85 x 56 x 30mm)
- Mount the rotary encoder, 4 buttons, and OLED display on the front panel
- Provide adequate ventilation (the Pi 4 runs warm, especially during DSP)
- Route audio cables (RCA from DAC HAT) and power cable (USB-C) out the back
- Allow access to the Ethernet port and USB ports

### 7.2 Layout

```
Front Panel:
┌──────────────────────────────────────────┐
│                                          │
│   ┌──────────────┐    [Prev] [Play] [Next]
│   │ OLED Display │                [Func] │
│   │  128x64      │                       │
│   └──────────────┘         (Volume)      │
│                              ◎           │
│                           [Encoder]      │
└──────────────────────────────────────────┘

Rear Panel:
┌──────────────────────────────────────────┐
│  [USB-C  ] [Ethernet] [USB] [RCA L][RCA R]
│  (power)                    (audio out)  │
└──────────────────────────────────────────┘

Top/Side:
Ventilation slots or mesh for airflow
```

### 7.3 Manufacturing Options

**3D Printed**: If you have access to a 3D printer, this is the most flexible option. Design in FreeCAD, Fusion 360, or OpenSCAD. Print in PLA or PETG. You can iterate on the design as you refine the control layout.

**Laser-Cut Acrylic**: A sandwich design using layers of laser-cut acrylic works well. Cut front, back, and side panels, then bolt them together with standoffs. Clear acrylic looks great and lets you see the hardware inside (nerd cred).

**Off-the-Shelf Aluminum Case**: Several companies make extruded aluminum cases for the Pi with DAC HATs. HiFiBerry sells cases designed for their boards. You'd need to drill holes for the encoder, buttons, and display, but the base case gives a professional look.

**Wooden Box**: For the vintage-audio aesthetic. A simple wooden box with a routed front panel for the controls. Pair with a walnut or cherry finish and brass hardware for something that looks like it belongs next to your tube amp from Part 3.

### 7.4 Mounting Tips

- The rotary encoder mounts in a 7mm panel hole with a nut and washer. Tighten it firmly -- a wobbly volume knob feels terrible.
- The OLED display can be held with small dabs of hot glue on the corners, or mounted in a cutout with a 3D-printed bezel.
- The push buttons mount in 6mm panel holes (or use PCB-mount buttons on a small carrier board behind the panel).
- Leave at least 5mm of clearance between the Pi's top components and the case lid for airflow.
- Use nylon standoffs (M2.5) to mount the Pi -- they don't conduct electricity and won't short anything.

---

## 8. First Test

### 8.1 The Moment of Truth

1. Power on the Pi with everything connected
2. Wait for boot (~30-45 seconds)
3. The OLED should display "Pi Streamer" / "Ready"
4. Open Spotify on your phone
5. Connect to the Pi streamer in Spotify's device list
6. Play a song

**What you should see and hear**:
- Music from the DAC HAT's RCA outputs (connected to your amp/speakers)
- The OLED display showing the track name, artist, and volume
- The progress bar advancing

**Now test the controls**:
- Turn the rotary encoder -- volume should change, and the OLED volume bar should update
- Press the encoder -- audio should mute (volume bar drops to 0)
- Press it again -- audio should unmute to previous level
- Press Next -- track should skip
- Press Play/Pause -- playback should toggle

### 8.2 Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| No audio output | DAC HAT not configured | Check moOde Audio Config, verify I2S device is selected |
| Audio plays but controls don't work | GPIO conflict or script not running | Check `systemctl status pistreamer-volume` |
| Encoder direction is reversed | CLK and DT swapped | Swap GPIO 17 and 27 in the script (or swap the wires) |
| OLED display is blank | I2C not enabled or wrong address | Run `i2cdetect -y 1` to verify; check wiring |
| Display shows "No Connection" | MPD not running | Check `systemctl status mpd` |
| Volume changes are jerky | Debounce too aggressive | Reduce `bouncetime` in encoder callback |
| Buttons trigger multiple times | Debounce too short | Increase `DEBOUNCE_MS` or add more software debounce delay |
| Audio has noise/hum | Ground loop or poor power supply | Use the official Pi power supply; check for ground loops with your amp |
| Pi won't boot with DAC HAT | Incompatible device tree overlay | Boot without HAT, verify moOde config, then reattach |

### 8.3 Audio Quality Verification

Once everything is working, do a quick quality check:

1. Play a well-mastered recording you know well
2. Listen for: noise floor (should be silent between tracks), channel balance, frequency response (does it sound full-range?)
3. Compare to a direct source (phone → amp) as a sanity check

If the DAC HAT is working correctly, you should hear no difference between the Pi streamer and any other decent source feeding the same amplifier. The HiFiBerry DAC2 Pro is audibly transparent -- it measures better than your hearing can detect.

> **What happens if...** you hear a faint clicking or ticking from the speakers when the Pi is on but no music is playing? This is likely the Pi's internal clock or USB bus generating noise that couples into the audio path through the power supply or ground. Try: (1) use the official Pi power supply, (2) check that the DAC HAT is firmly seated, (3) in moOde, disable HDMI audio output (`/boot/config.txt`: `dtoverlay=vc4-kms-v3d,noaudio`), which can reduce system noise.

---

## 9. What You've Built

You now have a working music streamer with:

- **Spotify Connect**: Stream from any Spotify app on any device
- **AirPlay**: Stream from any Apple device
- **DLNA/UPnP**: Play your local library from a NAS
- **Physical volume knob**: Immediate, tactile, always works
- **Transport buttons**: Play/pause, skip, previous
- **OLED display**: Current track, artist, progress, volume
- **Independent services**: Each control runs as its own systemd service, starts on boot, restarts on failure

Total cost: ~$145. Total build time: 2-3 hours for hardware assembly, 1-2 hours for software configuration.

This is the foundation. In Chapter 29, we add CamillaDSP to turn this streamer into an active crossover with room correction. In Chapter 30, we evolve it into a full multi-room control hub. The physical controls you've built here carry forward into both builds -- they're the constant, reliable interface that makes the system usable regardless of what software complexity sits behind them.

---

## Summary

The Pi streamer build uses a Raspberry Pi 4 (or 5) with a HiFiBerry DAC2 Pro HAT for high-quality I2S audio output, running moOde Audio as the software platform. Physical controls -- a KY-040 rotary encoder for volume, four tactile buttons for transport, and an SSD1306 OLED display for track information -- connect to the GPIO pins through a stacking header above the DAC HAT.

Three Python scripts handle the controls: one for the rotary encoder (adjusts ALSA volume directly, bypassing any player application), one for transport buttons (sends MPD commands), and one for the OLED display (polls MPD for track info and renders to the display). Each runs as an independent systemd service that starts on boot and restarts on failure.

The key design principle: physical controls operate at the system level (ALSA, MPD), not the application level. They work regardless of which source is active -- Spotify, AirPlay, local files, or anything else that outputs through ALSA. If the web interface crashes, if the network goes down, if your phone dies -- the knob still turns, the buttons still work, and the display still shows what's playing.
