
# StarPi Vision  
**Horizon Estimation (FastSCNN) & Parachute Detection (YOLO)**

Questo repository raccoglie **ricerche, decisioni tecniche e una pipeline funzionante** per due task di visione artificiale pensati per **Raspberry Pi + camera**:

1. **Stima dell’orizzonte**
   - Segmentazione sky / non-sky
   - Estrazione robusta della linea di orizzonte
   - Target: CPU, real-time, robusto a roll/pitch

2. **Rilevamento del paracadute**
   - Object detection (YOLO)
   - Target: individuare paracadute (open/closed) in video reali
   - Target: CPU-friendly, eventualmente accelerabile

## Task 1 – Horizon Estimation

### Problema
Stimare l’orizzonte visivo in video reali (camera mobile). Alcune note:
- robusto a rumore, nuvole
- accurato nello specifica zona di lancio

### Approccio scelto
**Segmentazione semantica → sky mask → estrazione frontiera → fit linea**

Motivazioni:
- L’orizzonte coincide quasi sempre con il confine sky / non-sky
- La segmentazione è più stabile del direct regression
- Il post-processing geometrico è controllabile e interpretabile

### Perché FastSCNN
FastSCNN è stato scelto come **baseline di ricerca** perché:
- architettura lightweight (depthwise separable + pyramid pooling)
- progettato per real-time su device embedded
- pre-addestrato su Cityscapes (classe *sky* disponibile)

### Questioni aperte
- Serve fine-tuning su dominio reale (ottiche, fisheye, altitudine)?
- Binaria sky/non-sky o multiclasse?
- Robustezza con cielo coperto / controluce?
- Robustezza in caso di rotazione / movimenti bruschi?

## Task 2 – Parachute Detection

### Problema
Rilevare paracadute in video reali, con forti variazioni di:
- scala (molto piccoli o molto grandi)
- sfondo (cielo, terreno, nuvole)
- stato (aperto / chiuso / parzialmente visibile)

### Approccio scelto
**Object detection (bounding box)**

Motivazioni:
- semplice da addestrare
- output sufficiente per tracking, logging, decisioni a bordo

### Perché YOLO
YOLO è stato scelto come baseline perché:
- ottimo compromesso accuracy / velocità
- facile integrazione in pipeline video
- versioni small/nano adatte a Raspberry Pi

### Questioni aperte
- Esistenza / qualità di dataset pubblici
- Necessità di dataset sintetico


## Dataset – Stato delle ricerche

### Paracadute
- Dataset pubblici **scarsi o frammentari**
- Molto materiale video non etichettato (YouTube, test, POV)
- Probabile strategia:
  - raccolta video
  - annotazione manuale / semi-manuale
  - fine-tuning

### Orizzonte / Sky
- Dataset generici di segmentazione (es. Cityscapes) utili come pretraining
- Dominio reale diverso (ottica, altitudine, scena)
- Possibile fine-tuning con pochi dati mirati
- Probabile strategia:
	- raccolta video
	- annotazione manuale / semi-manuale
	- fine-tuning