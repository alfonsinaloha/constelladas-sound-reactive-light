# Modes LED

## Mode 0 — BPM
Palette 8 couleurs défilant au tempo. Détection onset par comparaison énergie instantanée vs enveloppe longue terme.

## Mode 1 — Chroma
FFT → 12 bins chroma lissés IIR → mélange additif RGB. Chaque note = une couleur sur le cercle chromatique.

## Mode 2 — YIN f0
Fréquence fondamentale par algorithme YIN sur signal temporel. Très précis sur voix ou instrument monophonique.

## Mode 3 — Auto R&B (machine à états)

| Section | Déclencheur | Effet |
|---|---|---|
| SECT_808 | transient_808 ou sub_energy fort | Flash pal_808, hold 3 frames |
| SECT_DROP | sm_drop élevé | Violet pulsant au BPM |
| SECT_VOCAL | sm_vocal élevé | Wash 3 oscillateurs sinusoïdaux |
| SECT_HAT | sm_hat élevé | Étincelle pal_hat, 4 frames |

Scores lissés IIR avec SCORE_ALPHA = 0.12.

## Mode 4 — Strobe (WS2812)
Flash réactif aux kicks 808. Decay : `strobe_env *= 0.72f`. Couleur via commande `cstr`.

## Mode 5 — Rotation (WS2812)
Point lumineux rotatif sur anneau 24 LEDs. Vitesse et longueur de traîne proportionnelles à l'énergie audio. Couleur via commande `crot`.
