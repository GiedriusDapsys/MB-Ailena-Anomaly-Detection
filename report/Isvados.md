# Išvados

## 1. Apibendrinimas
Šiame darbe automatizuotai aptiktos anomalijos MB Ailena pardavimų laiko eilutėse (mėnesio pjūvis per prekių grupes). 
Naudoti 4 modeliai: **Autoencoder (MSE)**, **Isolation Forest**, **OCSVM**, **LOF**. 
Bazinis slenkstis – **85-asis procentilis** (patikrintas jautrumas 70–95 %).

## 2. Metodika (trumpai)
- Duomenys: Mėnuo, Kiekis, Pirkimo suma EUR, Pardavimo suma EUR, Pelno stulpelis; išvalyta/užpildyta NaN (vidurkis).
- Laiko langai: mėnesio langai per grupę; lyginimas tarp modelių.
- Autoencoder – LSTM AE rekonstrukcijos MSE; IForest/OCSVM/LOF – klasikiniai outlier score’ai.
- Sprendimo taisyklė: score > slenkstis ⇒ anomalija; slenkstis = procentilis.

## 3. Rezultatai
- Modelių aptiktos anomalijos (agreguota):
  ![Anomalijų skaičius (@85th)](../anomaly_count_by_model.png)

- TOP-10 pagal kiekvieną modelį:
  - AE: [TOP10](tables/ae_top10.md)
  - Isolation Forest: [TOP10](tables/ifor_top10.md)
  - OCSVM: [TOP10](tables/ocsvm_top10.md)
  - LOF: [TOP10](tables/lof_top10.md)

- Modelių suvestinė: [model_summary](tables/model_summary.md)

> Pastaba: jei turi paveikslus `report/img/*`, čia įdėk papildomus grafikus/sensitivumo kreivę.

## 4. Verslo išvados ir rekomendacijos
- **Dažniausios anomalijos** pastebėtos prekių grupėse: *(įrašyk pagal savo TOP lenteles)*.
- **Anomalijų tipinė priežastis** (hipotezės): netipiniai kiekiai, kainų/pirkimo sumos šuoliai, akcijos/likvidacijos, apskaitos klaidos, netipiniai užsakymai.
- **Veiksmai**:
  1. Kas mėnesį generuoti anomalijų ataskaitą ir perduoti atsakingam asmeniui patikrai.
  2. Anomalijoms priskirti statusus (patvirtinta / atšaukta / klaida), laikyti žymes – tai leis mokytis slenkstį ir pagerinti modelių kokybę.
  3. Jei anomalija tikra – užregistruoti priežastį (duomenų kokybė, tiekimas, kainodara ir pan.) → grįžtamasis ryšys į procesų tobulinimą.

## 5. Apribojimai
- Nedidelis istorinis laikotarpis (vieneri metai) riboja aiškų sezoniškumo užfiksavimą.
- AE modeliui svarbi duomenų kokybė ir normalizavimas; mažai duomenų – galima permažai išmokti.
- Klasikiniai modeliai (IForest/OCSVM/LOF) neturi „verslo konteksto“ – rodo tik statistinį išskirtinumą.

## 6. Ką daryti toliau
- Slenksčio parinkimą pritaikyti kiekvienai grupei atskirai; įvesti „anomalijų pajamingumą“ (kiek € reikšminga).
- Įtraukti papildomus požymius (pvz., pardavimo kanalai, klientų segmentai) – pagerins aiškinamumą.
- Sukurti mažą **MLOps** rutiną: savaitinis skriptas + pranešimai (pvz., el. paštu ar Slack).

## 7. Reprodukcija
- Notebook: [`notebooks/MB Ailena Anomaly Detection Report.ipynb`](../notebooks/MB%20Ailena%20Anomaly%20Detection%20Report.ipynb)
- Atvaizdai: `../anomaly_count_by_model.png` ir `img/*`
- Lentelės: `tables/*`

