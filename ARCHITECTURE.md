# Architecture technique

## Réseau multi-unités

```
        TP-Link AC750 (AP)
        192.168.0.1
              │
    ┌─────────┼─────────┐
    │         │         │
ESP32-S3   WS2812     TinyS3D
.102       .101       .103
LED 4W     Anneau     LED 4W
/control   24 LEDs    antenne ext.
    │
    ▼
iPhone — http://192.168.0.102/control
```

## Pipeline audio

```
INMP441 I2S
    │  44100 Hz / 24 bits
    ▼
pcm_buf[512] int32
    │
    ├──► FFT 512 pts (dsps_fft2r_fc32)
    │         │
    │         ├──► Chroma bins [12] ──────► Mode 1
    │         ├──► BPM onset detector ────► Mode 0
    │         ├──► YIN f0 pitch ──────────► Mode 2
    │         └──► Descripteurs spectraux ─► Mode 3
    │                   │
    │              sub_energy
    │              hi_ratio
    │              centroid_bin
    │              flatness
    │              transient_808
    │
    └──► audio_queue ──► led_task (Core 1)
```

## Commandes HTTP POST /cmd

```json
{"t":"mode",  "v":3}
{"t":"bri",   "v":0.8}
{"t":"spd",   "v":0.5}
{"t":"vc0",   "v":"#0064ff"}
{"t":"vc1",   "v":"#b400ff"}
{"t":"vc2",   "v":"#00dcb4"}
{"t":"c808",  "v":"#ffdcb4"}
{"t":"chat",  "v":"#ffb41e"}
{"t":"cstr",  "v":"#ffffff"}
{"t":"crot",  "v":"#ff00ff"}
```
