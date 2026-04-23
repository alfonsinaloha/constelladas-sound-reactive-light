# Hardware

## INMP441 — Microphone I2S MEMS
- Interface : I2S standard
- Fréquence : 44100 Hz
- Résolution : 24 bits
- Alimentation : 3.3V
- SNR : 61 dB

## LED puissance 4W RGBW
- 4 canaux : Rouge, Vert, Bleu, Blanc
- Optimisation flux : lentille focale 45° + pourtour réfléchissant
- Distance gobo optimale : 5-15cm de la lentille

## WS2812 anneau 24 LEDs
- Format : GRB (pas GRBW)
- 24 LEDs adressables individuellement
- GPIO : 5

## TinyS3D — antenne externe
```c
gpio_set_level(38, 1);  // HIGH = u.FL externe, LOW = antenne interne
```

## TP-Link TL-WR902AC
- Mode : Access Point
- Bande : 2.4GHz uniquement
- Mode WiFi : 11bg mixte (pas N)
- Largeur canal : 20MHz fixe
- Canal : 6
