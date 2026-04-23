[README.md](https://github.com/user-attachments/files/27021830/README.md)
# Constelladas — Système Lumineux Réactif au Son

> Un système d'éclairage modulaire, autonome et personnalisable, conçu pour que les artistes subliment leur performance sans dépendre d'un technicien.

**Langage** : C (ESP-IDF v5.4) · **Plateforme** : ESP32-S3 · **Protocoles** : WiFi HTTP OSC · **Audio** : I2S INMP441

---

## Présentation

Constelladas est un projet de design qui combine électronique embarquée, traitement audio temps réel et design d'objet pour créer un système d'éclairage scénique accessible, économique et transportable.

Le système analyse le son en temps réel (FFT, BPM, YIN, descripteurs spectraux) et pilote des LEDs adressables de manière réactive et musicale. L'artiste contrôle tout depuis son iPhone via une interface web embarquée directement dans l'ESP32 — aucune application à installer.

### Contexte artistique

Ce projet s'inscrit dans une réflexion sur l'autonomie des artistes indépendantes, notamment latinoaméricaines. Il dialogue avec le travail de Jeffrey Gibson sur l'ornement culturel et la survivance, et avec la pratique de l'artiste chilienne **La Nenita**, pour qui les premières extensions du système ont été créées.

Le motif bitmap qui caractérise l'identité visuelle de Constelladas est une broderie numérique — un dégradé transformé en trame de pixels, comme un tejido mapuche traduit en langage machine. La tension entre le technologique (ESP32, FFT, algorithme) et l'artisanal (papier découpé, extension main) n'est pas une contradiction — c'est le propos.

---

## Matériel

### Par unité

| Composant | Référence | Prix est. |
|---|---|---|
| Microcontrôleur | ESP32-S3 N8R8 | ~8€ |
| Microphone I2S | INMP441 | ~3€ |
| LED puissance | 4W RGBW | ~5€ |
| LED anneau | WS2812 24 LEDs (WS2812 seulement) | ~8€ |
| Impression 3D boîtier | PLA | ~20-30€ |

**Total estimé par unité : 45-60€**

### Réseau

| Composant | Configuration |
|---|---|
| TP-Link TL-WR902AC | Mode AP, 2.4GHz, canal 6, 20MHz, 11bg mixte |

---

## Connexions PIN

### ESP32-S3 principal (LED puissance 4W)

```
INMP441          ESP32-S3
BCLK  ────────►  GPIO 12
WS    ────────►  GPIO 14
SD    ────────►  GPIO 13
VDD   ────────►  3.3V
GND   ────────►  GND

LED 4W RGBW
Signal ───────►  GPIO 5
```

### ESP32-S3 WS2812 (anneau 24 LEDs)

```
INMP441          ESP32-S3
BCLK  ────────►  GPIO 12
WS    ────────►  GPIO 14
SD    ────────►  GPIO 13

WS2812 24 LEDs
Signal ───────►  GPIO 5
```

### TinyS3D

```
INMP441          TinyS3D
BCLK  ────────►  GPIO 1
WS    ────────►  GPIO 3
SD    ────────►  GPIO 2

LED 4W RGBW
Signal ───────►  GPIO 21

Antenne externe
RF Switch ────►  GPIO 38  (HIGH = u.FL)
```

---

## Installation

### 1. Prérequis

```bash
# Python 3.9+
python3 --version

# macOS — installer cmake
brew install cmake
```

### 2. ESP-IDF v5.4

```bash
git clone --recursive https://github.com/espressif/esp-idf.git ~/esp-idf-p4
cd ~/esp-idf-p4
git checkout v5.4
git submodule update --init --recursive
./install.sh esp32s3
```

### 3. Cloner ce repo

```bash
git clone https://github.com/TON_USERNAME/constelladas-sound-reactive-light.git
cd constelladas-sound-reactive-light
```

### 4. Configurer le WiFi

Dans chaque `firmware/*/main/main.c` :

```c
#define WIFI_SSID  "TP-Link_7A07"
#define WIFI_PASS  "ton_mot_de_passe"
```

### 5. Compiler et flasher

```bash
source ~/esp-idf-p4/export.sh

# ESP32-S3 principal
cd firmware/esp32-s3-main
idf.py build && idf.py -p /dev/cu.XXXX flash monitor

# WS2812
cd firmware/esp32-s3-ws2812
idf.py build && idf.py -p /dev/cu.XXXX flash monitor

# TinyS3D
cd firmware/tinys3
idf.py build && idf.py -p /dev/cu.XXXX flash monitor
```

Le port `/dev/cu.XXXX` s'identifie avec `ls /dev/cu.*`

### 6. Interface iPhone

```
http://[IP_ESP32_PRINCIPAL]/control
```

L'IP s'affiche dans le monitor au démarrage :
```
led_chroma: IP: 192.168.0.102  OSC port 8000
```

Pour ajouter à l'écran d'accueil : **Safari → Partager → Sur l'écran d'accueil**

---

## Modes LED

| # | Nom | Description |
|---|---|---|
| 0 | BPM | Palette 8 couleurs synchronisée aux beats |
| 1 | Chroma | 12 notes chromatiques → couleur |
| 2 | YIN f0 | Fréquence fondamentale → couleur |
| 3 | Auto R&B | Machine à états : 808 / Drop / Vocal / Hihat |
| 4 | Strobe | Flash réactif aux kicks (WS2812) |
| 5 | Rotation | Point lumineux rotatif réactif à l'énergie (WS2812) |

---

## Architecture technique

```
Core 0                     Core 1
─────────────────────      ──────────────────────
I2S → PCM buffer           led_task
FFT 512 pts (esp-dsp)        └─ modes 0-5
Chroma 12 notes            ▲
BPM detector               │ audio_queue
YIN pitch f0               │ (xQueueSend)
Descripteurs spectraux     │
HTTP server :80  ──────────┘
OSC UDP :8000
```

---

## Contrôle OSC

Port UDP **8000** — compatible Max/MSP, TouchOSC, Pure Data.

```
/mode       ,i    0-5
/brightness ,f    0.0-1.0
```

Broadcast toutes les unités : `192.168.0.255:8000`

---

## Historique

| Phase | Technologie | Statut |
|---|---|---|
| 1 | MicroPython ESP32 | Prototype — latence trop élevée |
| 2 | Arduino C++ | Intermédiaire — limitations DSP |
| 3 | ESP-IDF C natif | Production — latence < 16ms |

---

## Structure

```
constelladas-sound-reactive-light/
├── README.md
├── docs/
│   ├── ARCHITECTURE.md
│   ├── MODES.md
│   ├── OSC.md
│   └── HARDWARE.md
├── hardware/
│   └── schemas/
├── firmware/
│   ├── esp32-s3-main/     ← LED 4W + page /control
│   ├── esp32-s3-ws2812/   ← Anneau 24 LEDs
│   └── tinys3/            ← TinyS3D antenne externe
└── interface/
    └── control.html       ← Interface iPhone standalone
```

---

## Licence

MIT — libre d'utilisation, modification et distribution.

---

## Crédits

Développé par **Alfonsina Fernandez** — Diplôme de design, 2026.  
Première application : artiste **La Nenita** (Chili).

*"Pour les artistes qui brillent seules."*
