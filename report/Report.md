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
