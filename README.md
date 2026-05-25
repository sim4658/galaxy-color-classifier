# 🌌 Galaxy Color Classifier
### Classificazione automatica di galassie con Machine Learning su dati reali SDSS

![Python](https://img.shields.io/badge/Python-3.11-blue?style=flat-square&logo=python)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-orange?style=flat-square&logo=scikit-learn)
![SDSS](https://img.shields.io/badge/Data-SDSS%20DR18-purple?style=flat-square)
![Accuracy](https://img.shields.io/badge/Accuracy-98.3%25-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

---

## 📖 Descrizione

Questo progetto applica tecniche di **Machine Learning** alla classificazione morfologica di galassie, utilizzando dati fotometrici reali provenienti dalla **Sloan Digital Sky Survey (SDSS)**.

L'obiettivo è distinguere automaticamente galassie **rosse** (ellittiche, vecchie, con poca formazione stellare) da galassie **blu** (spirali, giovani, con formazione stellare attiva) basandosi esclusivamente sulle loro misure di luminosità in 5 bande fotometriche: **u, g, r, i, z**.

Il risultato è un classificatore con **98.3% ± 0.5% di accuratezza** (cross-validation a 5 fold), che scopre autonomamente che le bande **ultravioletta (u)** e **infrarossa (z)** sono i discriminatori fisici più informativi — in accordo con la letteratura astrofisica.

---

## 🔭 Contesto Fisico

Le galassie si distribuiscono in modo **bimodale** nello spazio dei colori fotometrici — un risultato osservativo robusto noto come *red sequence / blue cloud* (Baldry et al. 2004, Blanton et al. 2003):

- **Galassie rosse** → alta magnitudine nella banda u, bassa nella banda z → poca luce UV → poca formazione stellare → galassie ellittiche, vecchie, passive
- **Galassie blu** → bassa magnitudine nella banda u → molta luce UV → formazione stellare attiva → galassie spirali, giovani, ricche di gas

La banda **u** (ultravioletto) è particolarmente sensibile alla presenza di stelle giovani e calde, rendendola il predittore più potente del tipo morfologico — come confermato dalle feature importance del modello.

---

## 📊 Dataset

| Proprietà | Valore |
|---|---|
| Fonte | Sloan Digital Sky Survey (SDSS) |
| Oggetti totali | 2.000 galassie |
| Oggetti dopo cleaning | ~1.997 |
| Redshift range | 0.01 – 0.30 |
| Magnitudine r | 14 – 19 |
| Feature utilizzate | u, g, r, i, z |
| Target | Rossa (g-r > 0.6) / Blu (g-r ≤ 0.6) |
| Distribuzione classi | ~86% rosse, ~14% blu |

I dati vengono scaricati in tempo reale tramite query SQL al database pubblico SDSS attraverso la libreria `astroquery`.

---

## 🧠 Metodologia

```
Dati SDSS (SQL)
      │
      ▼
Data Cleaning          → rimozione valori sentinella (-9999)
      │
      ▼
Feature Engineering    → calcolo colore fotometrico g-r
      │
      ▼
Train/Test Split       → 80% training, 20% test
      │
      ▼
Random Forest          → 100 alberi decisionali
      │
      ▼
Cross-Validation       → 5 fold, metrica: accuracy
      │
      ▼
Feature Importance     → analisi fisica dei predittori
      │
      ▼
PCA + Decision Boundary → visualizzazione 2D dei confini
```

### Perché Random Forest?

Il **Random Forest** è un ensemble di 100 alberi decisionali, ognuno allenato su un sottoinsieme casuale dei dati. La predizione finale è determinata dal voto della maggioranza. Questo approccio garantisce:

- **Robustezza** rispetto all'overfitting
- **Interpretabilità** tramite feature importance
- **Nessuna assunzione** sulla distribuzione dei dati
- **Gestione naturale** dello sbilanciamento tra classi

---

## 📈 Risultati

### Performance del modello

| Metrica | Galassie Blu | Galassie Rosse |
|---|---|---|
| Precision | 0.96 | 0.98 |
| Recall | 0.90 | 0.99 |
| F1-score | 0.93 | 0.99 |
| **Accuratezza totale** | **98.3% ± 0.5%** | |

### Feature Importance

| Banda | Importanza | Interpretazione fisica |
|---|---|---|
| u (ultravioletto) | 0.336 | Traccia la formazione stellare attiva |
| z (infrarosso) | 0.246 | Traccia la massa stellare totale |
| i (infrarosso vicino) | 0.161 | Luminosità delle stelle vecchie |
| g (verde) | 0.141 | Luce delle stelle di popolazione mista |
| r (rosso) | 0.116 | Complementare alla banda g |

### Esperimento: il redshift migliora la classificazione?

Aggiungendo il redshift come feature aggiuntiva, l'accuratezza rimane **98.3% ± 0.5%** — identica al modello senza redshift. Nel range z = 0.01–0.30 l'evoluzione galattica non è ancora sufficiente a introdurre informazione aggiuntiva. Le bande fotometriche catturano già tutta l'informazione disponibile in questo volume cosmico.

---

## 🛠️ Installazione e utilizzo

### Prerequisiti

```bash
Python 3.9+
Anaconda (consigliato)
```

### Installazione dipendenze

```bash
pip install astroquery astropy matplotlib pandas scikit-learn numpy
```

### Esecuzione

Apri il notebook in Jupyter:

```bash
jupyter notebook galaxy_classifier.ipynb
```

Esegui le celle in ordine. Il notebook scarica automaticamente i dati da SDSS — è necessaria una connessione internet per la cella di download.

---

## 📁 Struttura del progetto

```
galaxy-color-classifier/
│
├── galaxy_classifier.ipynb   # Notebook principale con tutto il codice
├── README.md                 # Questo file
└── requirements.txt          # Dipendenze Python
```

---

## 🔬 Possibili sviluppi futuri

- **Dataset più ampio** — aumentare a 50.000+ galassie per migliorare la robustezza sulle galassie blu (classe minoritaria)
- **Redshift elevato** — estendere il campione a z > 1 per studiare l'evoluzione galattica nel tempo cosmico
- **Classificazione morfologica** — aggiungere le classi spirale/ellittica/irregolare usando i dati del catalogo Galaxy Zoo
- **Reti neurali** — confrontare Random Forest con una CNN applicata direttamente alle immagini SDSS
- **Fotometria a banda stretta** — integrare dati spettroscopici per predire l'età stellare media

---

## 📚 Riferimenti

- Baldry et al. (2004) — *"Quantifying the Bimodal Color-Magnitude Distribution of Galaxies"*, ApJ
- Blanton et al. (2003) — *"The Galaxy Luminosity Function and Luminosity Density at Redshift z = 0.1"*, ApJ
- [SDSS Data Release 18](https://www.sdss.org/dr18/)
- [AstroPy Documentation](https://www.astropy.org/)
- [scikit-learn Documentation](https://scikit-learn.org/)

---

## 👤 Autore

Progetto sviluppato come primo progetto personale di **data science applicata all'astrofisica**, durante il percorso di laurea triennale in Fisica.

---

*"The universe has its data — go analyze it."* 🔭
