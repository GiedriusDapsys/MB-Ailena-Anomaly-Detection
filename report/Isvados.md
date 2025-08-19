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
### Ataskaitos lentelės
- [Modelių santrauka](report/model_summary.md)
- [AE – TOP 10](report/ae_top10.md)
- [Isolation Forest – TOP 10](report/ifor_top10.md)
- [OCSVM – TOP 10](report/ocsvm_top10.md)
- [LOF – TOP 10](report/lof_top10.md)

### Slengsčio jautrumas
[![Jautrumo kreivė](report/anomaly_count_by_model.png)](report/isvados.md)


## 4. Verslo išvados ir rekomendacijos
- **Dažniausios anomalijos** pastebėtos prekių grupėse: *(įrašyk pagal savo TOP lenteles)*.
- **Anomalijų tipinė priežastis** (hipotezės): netipiniai kiekiai, kainų/pirkimo sumos šuoliai, akcijos/likvidacijos, apskaitos klaidos, netipiniai užsakymai.
- **Veiksmai**:
  1. Kas mėnesį generuoti anomalijų ataskaitą ir perduoti atsakingam asmeniui patikrai.
  2. Anomalijoms priskirti statusus (patvirtinta / atšaukta / klaida), laikyti žymes – tai leis mokytis slenkstį ir pagerinti modelių kokybę.
  3. Jei anomalija tikra – užregistruoti priežastį (duomenų kokybė, tiekimas, kainodara ir pan.) → grįžtamasis ryšys į procesų tobulinimą.
 
  # Išvados

**Sąranka.** 4 modeliai (AE_MSE, Isolation Forest, OCSVM, LOF), slenkstis – **85-asis procentilis**.

## 1) Kiek anomalijų aptiko kiekvienas modelis
- **AE_MSE:** 3  
- **IForest:** 3  
- **OCSVM:** 3  
- **LOF:** 2  

> Išvada: LOF aptiko viena anomalija mažiau, lyginant su kitais trim modeliais – jis elgiasi **konservatyviau**.

## 2) Bendras unikalių anomalijų skaičius
- **3 unikalūs anomalijų langai** (grupė + mėnuo).  
  Tai reiškia, kad sudėjus visų modelių radinius, gauname 3 skirtingus atvejus be dubliavimosi.

## 3) Dažniausiai pasikartojančios prekių grupės tarp modelių
- **Stretch gatava produkcija – 4 kartai**  
- **Termo pvc – 4 kartai**  
- **Šiltinimo medžiagos – 3 kartai**

> Išvada: „Stretch gatava produkcija“ ir „Termo pvc“ **nuosekliai iškrenta kaip anomalijos** skirtingų modelių akimis – šias grupes verta prioritetiškai peržiūrėti.

## 4) Modelių persidengimas (Jaccard)
- **AE_MSE vs IForest:** 1.00  
- **AE_MSE vs OCSVM:** 1.00  
- **AE_MSE vs LOF:** 0.67  
- **IForest vs OCSVM:** 1.00  
- **IForest vs LOF:** 0.67  
- **OCSVM vs LOF:** 0.67  

> Išvada: **AE_MSE, IForest ir OCSVM sutampa 100%** (randa tuos pačius 3 langus).  
> **LOF sutampa ~67%** su kiekvienu iš jų (2 iš 3 langų).  
> Praktikoje tai reiškia, kad **LOF – konservatyvesnis** ir praleidžia vieną iš bendrų atvejų.

---

## Rekomendacijos

1. **Ansamblio taisyklė.** Fiksuoti anomaliją, kai **sutaria ≥2 modeliai**. Mūsų rezultate visi trys langai buvo aptikti bent 3 modelių – jie patikimi.  
2. **LOF peržiūra.** Vieną „bendrą“ atvejį LOF praleido – **peržiūrėti tą laikotarpį**: ar tai LOF jautrumo klausimas, ar specifinis signalas, kuriam reikėtų kitų bruožų.
3. **Slenksčio jautrumas.** Pabandyk **80th ir 90th** procentilius – pamatysime, ar gaunama daugiau/mžiau prasmingų atvejų ir kaip keičiasi persidengimai.
4. **Papildomi požymiai.** Įtraukti **maržą, kliento tipą, grąžas, akcijas, sezoniškumą** – tai gali padidinti modelių atskyrimo galią.
5. **Operaciniai veiksmai.**  
   - „Stretch gatava produkcija“ ir „Termo pvc“ – **prioritetinė patikra** (pirkimo/pardavimo kainos, atsargų dinamika, tiekimo pokyčiai).  
   - „Šiltinimo medžiagos“ – **antros bangos** peržiūra.

---

## Vienos eilutės santrauka (README)

> **Prie 85-ojo procentilio aptikti 3 unikalūs anomalijų langai; AE_MSE, IForest ir OCSVM sutapo 100%, LOF – 67%. Dažniausiai kartojasi „Stretch gatava produkcija“ ir „Termo pvc“. Rekomenduojama ansamblio taisyklė ≥2 modelių sutarimui.**


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

