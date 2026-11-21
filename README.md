# StarPi — AI / CV / Sensor Fusion: idee, dataset e codice open source

Una raccolta di idee, dataset e repository open source da cui prendere spunto per applicazioni di computer vision, machine learning e sensor fusion al progetto StarPi.

L'obiettivo è esplorare soluzioni già disponibili, capire cosa potrebbe essere riutilizzato e suggerire qualche esperimento interessante, sia per il payload Raspberry Pi + camera sia per l'analisi dei dati avionici.

> Risorse consultate a novembre 2026. Sono punti di partenza, non soluzioni già validate per StarPi: disponibilità, licenze e compatibilità vanno verificate prima dell'utilizzo.

## 1. Horizon estimation: sky mask → frontiera → linea

**Idea:** usare una segmentazione sky/non-sky per estrarre una frontiera robusta. Da confrontare con metodi OpenCV molto più semplici (soglie colore, edge detection, RANSAC). Una sky/terrain boundary non coincide sempre con l'orizzonte geometrico; il fisheye va calibrato.

| Risorsa | Cosa contiene / perché utile |
|---|---|
| [Fast-SCNN PyTorch](https://github.com/Tramac/Fast-SCNN-pytorch) | Implementazione di FastSCNN, adatta come base di esperimenti. |
| [MMSegmentation: FastSCNN](https://github.com/open-mmlab/mmsegmentation/tree/main/configs/fastscnn) | Configurazioni e modelli per benchmark riproducibili. |
| [SkyFinder / Zenodo](https://zenodo.org/records/5884485) | Sky segmentation da webcam; utile per iniziare, ma differente da video di volo. |
| [MaSTr1325](https://www.vicos.si/resources/mastr1325/) | 1.325 immagini annotate con cielo, mare e ambiente; interessante per lo studio delle frontiere. |
| [Sky-Segmentation](https://github.com/ChengChen-ai/Sky-Segmentation) | Dataset sky e codice di un altro modello. |
| [Monocular Horizon Detector](https://github.com/prestonbrich/monocular-horizon-detector) | Proof of concept per orientamento da orizzonte marino: utile per geometria, confidence gating e failure cases. Parametri camera assunti; fusion con IMU non implementata. |

**Mini-esperimenti:** (a) FastSCNN pretrained su video propri, (b) annotare 30–50 frame difficili, (c) RANSAC sulla frontiera, (d) immagini ruotate e senza orizzonte visibile, (e) benchmark FPS/RAM su Pi reale.

**Attenzione:** SkyFinder e MaSTr1325 non sono dataset raccolti da razzi. Da usare per primi test, non come prova di generalizzazione.

## 2. Parachute detection: YOLO + tracking

**Idea:** partire da un detector con **una sola classe** `parachute`, poi distinguere `open/partially_open/closed` se le immagini consentono annotazioni coerenti. Il paracadute chiuso potrebbe non essere riconoscibile in modo affidabile.

| Risorsa | Cosa contiene / perché utile |
|---|---|
| [Airborne Object Detection Dataset](https://github.com/FreshtaSama/Airborne-Object-Detection-Dataset) | Il README dichiara **80.179 immagini complessive** in cinque classi, tra cui `parachute`, con bounding box YOLO. Da verificare numero effettivo di paracaduti, disponibilità dei file, provenienza, qualità e licenza. |
| [Multi-Background Airborne Object Detection (MBAOD)](https://data.mendeley.com/datasets/zfc8hndczp/1) | Dataset 2026 con sei classi, tra cui `parachute`, e più tipi di sfondo; include anche test con meteo degradato. Pubblicato con licenza CC BY 4.0; controllare contenuto e origine delle immagini. |
| [Paraşüt su Roboflow](https://universe.roboflow.com/wolkngndz/parasut) | Dataset candidato per un piccolo test di fine-tuning, da verificare. |
| [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) | Training, inferenza, esportazione dei modelli. Verificare la licenza del software e dei pesi scelti. |
| [YOLO su Raspberry Pi](https://docs.ultralytics.com/guides/raspberry-pi/) | Guida al deployment e ai benchmark; NCNN è un backend da provare. |
| [Roboflow Trackers](https://github.com/roboflow/trackers) | Esempi di tracking da associare alle detection frame-by-frame. |

**Mini-esperimenti:** (a) controllare 100 immagini pubbliche e le annotazioni, (b) aggiungere frame di voli reali, (c) split per video e non per singolo frame, (d) misurare recall per oggetti piccoli e falsi positivi/minuto, (e) tracking temporale.

**Questioni aperte:** vista da camera onboard o da terra? Quanto grande è il paracadute in pixel? È visibile durante l'apertura o solo dopo?

## 3. Sensor fusion / avionica: esplorazioni su log e simulazioni

**Idea:** confrontare una baseline fisica (filtraggio, EKF, controllo degli outlier) con eventuali correzioni ML **offline**.

| Risorsa | Cosa contiene / perché utile |
|---|---|
| [EuRoC 2021 team flight data](https://github.com/catsystems/euroc21-team-data) | Repository pensato per condividere log di voli EuRoC: punto di partenza per esplorare formati e segnali disponibili. |
| [RocketPy](https://rocketpy-team.github.io/) | Simulazioni del volo di razzi, analisi di incertezza e scenari atmosferici. |
| [OpenRocket](https://github.com/openrocket/openrocket) | Progettazione e simulazione di razzi, utile per scenari baseline. |
| [RocketryVT — Active Drag System](https://github.com/RocketryVT/active-drag-system) | Codice di un sistema di aerofreni basato su Raspberry Pi Pico; interessante da esplorare insieme alla [panoramica avionica del team](https://github.com/RocketryVT/Avionics), che descrive l'uso di IMU, barometro, GPS, magnetometro e filtro di Kalman. |
| [OpenAvionics](https://github.com/AP343hack/OpenAvionics) | Esempio didattico di simulazione avionica, Kalman filter, stato di volo e dashboard. |
| [Guided Rocket Avionics Simulation](https://github.com/Guided-Rocket-Avionics/Simulation) | Esempi di simulazione da esplorare; non trattare come software di volo già validato. |

**Spunti:** grafici GPS vs barometro; stima quota e velocità con un filtro semplice; residui e anomalie; stress test con dropout GPS, saturazione IMU o bias termici sintetici. Il lavoro più utile potrebbe essere un notebook che carica un log e produce grafici standard.

## 4. Altri team e repo da cui prendere ispirazione

| Team / repo | Cosa osservare |
|---|---|
| [USN Horizon](https://github.com/USN-Horizon) / [Avionics](https://github.com/USN-Horizon/Avionics) | Team universitario che documenta un progetto avionico per EuRoC 2027: flight computer, ground station, telemetria e HIL. |
| [Rocketry at Virginia Tech / Avionics](https://github.com/RocketryVT/Avionics) | Firmware di volo, air brakes, organizzazione dei repository e dati di test disponibili. |
| [Rice Eclipse](https://github.com/rice-eclipse) | Progetti pubblici di avionica e payload di un team universitario. |
| [CU InSpace](https://github.com/CarletonURocketry) | Software e hardware per avionica e payload. |
| [ICL Rocketry](https://github.com/icl-rocketry) | Ecosistema avionico universitario. |
| [USN Horizon — altri repository](https://github.com/USN-Horizon) | Esplorare i progetti pubblici del gruppo per altre idee. |
| [AstraNova 2025–26](https://github.com/SaltyNebula/AstraNova_2025-2026) | Organizzazione per sottosistemi, recovery, payload, avionics e design review. |

**Nota:** non sono tutti team EuRoC, né sviluppano tutti computer vision. Sono soprattutto esempi di organizzazione, documentazione e software da esplorare.

## 5. Idee bonus

- **Video + telemetria sincronizzati:** sovrapporre su un filmato quota, velocità, fase di volo e timestamp. Utile anche senza rete neurale.
- **Flight replay notebook:** visualizzare la stessa traiettoria usando GPS, barometro, IMU e simulazione RocketPy.
- **Quality/confidence dell'orizzonte:** segnalare quando l'algoritmo non dovrebbe fidarsi della previsione.
- **Parachute timeline:** generare una timeline della prima detection, delle perdite di detection e della dimensione della bounding box (solo diagnostica).
- **Synthetic augmentation mirata:** blur, rotazioni, ombre, cielo coperto e distorsioni della camera; verificare sempre su dati reali.

## 6. Punti da verificare prima di riutilizzare le risorse

- **Licenze:** pubblico o open access non significa libero per qualsiasi utilizzo. Verificare separatamente dataset, codice e pesi.
- **Dataset shift:** webcam, riprese aeree e video onboard rocket sono domini diversi. Raccogliere dati rappresentativi della camera e dello scenario reali.
- **Data leakage:** split train/test per **volo**, sessione o video; evitare frame vicini distribuiti casualmente tra gli split.
- **Hardware:** misurare FPS, latenza, RAM e stabilità sull'hardware di volo.
- **Safety:** mantenere modelli CV/ML sperimentali separati dai sistemi critici di recupero e dalle relative decisioni.

## 7. Da dove inizierei

1. **Vision:** aprire FastSCNN + SkyFinder e i dataset di paracaduti; provare una baseline su qualche frame reale.
2. **Avionica:** aprire EuRoC 2021 flight data + RocketPy + repository Virginia Tech; capire quali log, script e notebook sono riutilizzabili.

**Fonti di contesto:** [EuRoC competition rules](https://euroc.pt/competition-rules/).
