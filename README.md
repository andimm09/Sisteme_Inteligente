

## Predicția rezultatului meciurilor de League of Legends la minutul 10

### 1. Sursa datelor
* **Dataset:** League of Legends SoloQ matches at 10 minutes (2024) 
* **Link:** [Kaggle Dataset Link](https://www.kaggle.com/datasets/karlorusovan/league-of-legends-soloq-matches-at-10-minutes-2024) 
* **Data descărcării:** 26 Martie 2026 

---

### 2. Obiectivul proiectului
Scopul acestui proiect este de a implementa un model de clasificare capabil să prezică echipa câștigătoare (Blue/Red) într-un meci de league of legends, folosind exclusiv statisticile acumulate în primele 10 minute de joc. 
Vom analiza impactul indicatorilor precum:
* Diferența de Gold și Experiență;
* Obiectivele neutre (Dragons, Heralds);
* Numărul de kill-uri și asisst-uri

---

### 3. Tehnologii utilizate
* **Limbaj:** Python 
* **Biblioteci:** `pandas` pentru manipularea datelor, `scikit-learn` pentru modelare 

---
### 4. Analiza Exploratorie a Datelor (EDA)

În faza de analiză exploratorie, am investigat relația dintre diferiți indicatori la minutul 10 și rezultatul final al meciului. Câteva concluzii importante din graficele generate:

* **Matricea de Corelație (Heatmap):** Analiza arată că cei mai puternici predictori pentru o victorie a echipei Blue sunt **Aurul Total (Total Gold)** (corelație de 0.45) și **Experiența Totală (Total XP)** (0.44). Numărul de kill-uri este important (0.39), dar economia și experiența dictează ritmul jocului. În mod natural, aurul și experiența echipei adverse au o corelație negativă la fel de puternică.
* **Impactul Aurului (Boxplot):** Distribuția aurului ne arată clar că, în meciurile câștigate, echipa Blue reușește să strângă o cantitate mediană de aur semnificativ mai mare în primele 10 minute, comparativ cu meciurile în care au fost învinși.
* **Aur vs. Experiență (Scatterplot):** Vizualizarea dispersiei confirmă o zonă de „dominare clară”: meciurile în care echipa Blue reușește să controleze atât avantajul de aur, cât și pe cel de experiență (cadranul dreapta-sus), sunt predominant victorii.

---
**Autor:** Băbuț Ștefan Andrei
