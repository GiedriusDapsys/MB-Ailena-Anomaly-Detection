# MB Ailena – Anomaly Detection Report

## 1. Įvadas
- Tikslas: automatiškai aptikti pardavimų anomalijas pagal prekių grupes ir mėnesius.
- Nauda: ankstyvas nukrypimų aptikimas (tiekimas, kainodara, akcijos, klaidos).
- Duomenų šaltinis: mėnesinės suvestinės (Excel, 2025 m.).

## 2. Duomenys ir paruošimas
- Laukai: Grupė, Mėnuo, Kiekis, Pirkimo suma EUR, Pardavimo suma EUR, Pelnas.
- Valymas: meta-eilučių praleidimas, grupių pavadinimų ffill, tipų konvertavimas (float/datetime).
- Trūkstamų reikšmių tvarkymas: ffill/bfill.
- Normalizavimas (jei taikoma).

## 3. Metodika
- Laiko langai: 12 mėn. slenkantis langas.
- Modelis: LSTM Autoencoder (Keras).
- Treniruotė: 80% train, 20% test; nuostolis – MSE.
- Anomalijų slenkstis: MSE percentilė (pvz., 85-asis).
- Alternatyvos: MAD, z-score, Prophet/ARIMA likučiai.

## 4. Eksperimentai
- Hiperparametrai: latent_dim, epochs, batch_size.
- Jautrumo analizė: slenksčiai 70–95 %.
- Kryžminiai pjūviai: pagal prekių grupę.

## 5. Rezultatai
- Pivot (grupė × mėnuo) su anomalijų skaičiumi.
- Top-10 grupių pagal anomalijas.
- Laiko serijų grafikai (top-3 grupės) su pažymėtais anomalijų intervalais.
- Slenksčio įtaka (grafikas).

## 6. Verslo įžvalgos
- Labiausiai rizikingos grupės.
- Galimos priežastys (sezoniškumas, tiekimas, kainodara, akcijos).
- Operacinės rekomendacijos (užsakymų planavimas, kainų peržiūra).
- ### 06.1 Rezultatų santrauka (@85th)

![Anomalijų skaičius (@85th)](../anomaly_count_by_model.png)

**Modelių anomalijų skaičius:**
- AE_MSE: **3**
- Isolation Forest: **3**
- OCSVM: **3**
- LOF: **2**

**Bendras unikalių anomalijų langų (Grupė+Mėnuo) skaičius:** **3**

**Dažniausiai pasikartojančios prekių grupės (tarp modelių):**
- **Stretch gatava produkcija – 4 kartai**
- **Termo pvc – 4 kartai**
- **Šiltinimo medžiagos – 3 kartai**

**Poriniai modelių persidengimai (Jaccard):**
- AE_MSE ↔ IForest: **1.00**
- AE_MSE ↔ OCSVM: **1.00**
- AE_MSE ↔ LOF: **0.67**
- IForest ↔ OCSVM: **1.00**
- IForest ↔ LOF: **0.67**
- OCSVM ↔ LOF: **0.67**

**Trumpa išvada.** AE_MSE, Isolation Forest ir OCSVM sutampa 100% (randa tuos pačius 3 atvejus), LOF – šiek tiek konservatyvesnis (2/3). Prioritetinė patikra: *Stretch gatava produkcija* ir *Termo pvc*. Rekomendacija – laikyti anomalija, kai sutinka ≥2 modeliai (pradžiai 85th slenkstis, vėliau adaptuoti).


## 7. Išvados ir tolimesni darbai
- Sprendimo vertė: ankstyvas aptikimas ir veiksmų prioritetai.
- Tolesni žingsniai: daugiau duomenų (kanalai/regionai), modelio palyginimai, CI/CD, automatinės ataskaitos.

## 8. Priedai
- Modelio architektūra ir parametrai.
- Naudotos bibliotekos / versijos.
- Nuorodos į notebook’us ir grafikus.
# MB Ailena – Anomaly Detection

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GiedriusDapsys/MB-Ailena-Anomaly-Detection/blob/main/notebooks/MB%20Ailena%20Anomaly%20Detection%20Report.ipynb)

**Tikslas:** Automatizuotas pardavimų anomalijų aptikimas pagal prekių grupes ir mėnesius (MB Ailena).

## Struktūra
- `data/` – žali duomenys (Excel/CSV), **nekeliami į GitHub**.
- `notebooks/` – Colab/Jupyter analizės.
- `src/` – pagalbiniai skriptai.
- `report/` – rašytinė ataskaita (Markdown/PDF).

## Paleidimas Colab
1. Spauskite „Open in Colab“ (aukščiau).
2. Colab’e įkelkite Excel į `/content/` (Files → Upload).
3. Paleiskite notebook’ą nuo pradžios iki galo.
