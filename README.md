# Predictia rezultatului meciurilor de League of Legends la minutul 10

**Autor:** Babut Stefan Andrei  
**Proiect:** Analiza Datelor / Invatare Automata  

## Detalii Tehnice si Sursa Datelor
* **Dataset:** League of Legends SoloQ matches at 10 minutes (2024)
* **Link:** [Kaggle Dataset Link](https://www.kaggle.com/datasets/karlorusovan/league-of-legends-soloq-matches-at-10-minutes-2024)
* **Data descarcarii:** 26 Martie 2026
* **Tehnologii:** Python 3, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`, `shap`

---

## 1. Introducere si motivatia alegerii bazei de date

Domeniul jocurilor video a evoluat masiv in ultimul deceniu, trecand de la o simpla sursa de divertisment la o adevarata industrie competitiva, cunoscuta sub numele de e-sports. Unul dintre cele mai populare titluri la nivel global este League of Legends (LoL), un joc de tip MOBA (Multiplayer Online Battle Arena) in care doua echipe a cate cinci jucatori se confrunta pentru a distruge baza adversa. 

Motivatia din spatele alegerii acestei teme si a bazei de date aferente este complexitatea si volumul urias de date pe care un meci de League of Legends le genereaza in timp real. Jocurile de tip MOBA sunt cunoscute pentru un fenomen numit "snowball effect" (efectul de bulgare de zapada). Acest concept sugereaza ca un avantaj minor obtinut in primele minute ale jocului se acumuleaza exponential, oferind echipei respective mai mult aur si experienta, ceea ce face ca victoria sa fie din ce in ce mai probabila.

Proiectul de fata isi propune sa testeze aceasta ipoteza empiric, folosind algoritmi de Invatare Automata. Am ales sa analizez daca statisticile acumulate in primele 10 minute de joc sunt suficiente pentru a stabili cu exactitate ce echipa va castiga meciul la final.

---

## 2. Contextul sursei de date si cerintele proiectului

Pentru realizarea acestui proiect, datele au fost preluate de pe platforma Kaggle. Baza de date aleasa contine extrageri din API-ul oficial al jocului pentru mii de meciuri jucate la un nivel inalt de competitivitate (clasamentul SoloQ).

Setul de date brut a continut initial un numar de peste 24.200 de meciuri distincte. Pentru fiecare meci in parte, au fost inregistrate atat victoria finala (eticheta noastra de predictie), cat si zeci de variabile inregistrate fix la secunda in care ceasul jocului a atins minutul 10:00. 

### Structura Datelor
Pentru a asigura o procesare corecta, am analizat structura celor variabile disponibile la minutul 10. Principalele categorii de date extrase includ:

| Categorie | Nume Coloana | Explicatie |
| :--- | :--- | :--- |
| **Generale** | `matchId` | Identificatorul unic al meciului |
| **Generale** | `blueWin` | Variabila tinta (1 = victorie Blue, 0 = victorie Red) |
| **Economie** | `blueTeamTotalGold` | Aurul cumulat de echipa albastra |
| **Experienta**| `blueTeamXp` | Punctele de experienta cumulate |
| **Obiective** | `blueTeamDragonKills` | Numarul de dragoni doborati |
| **Agresiune** | `blueTeamTotalKills` | Numarul total de eliminari (kills) |
| **Viziune** | `blueTeamWardsPlaced` | Garzile de viziune plasate pe harta |

---

## 3. Implementare: Analiza Exploratorie a Datelor (EDA)

In faza de analiza exploratorie, am curatat datele (eliminand valorile nule si coloanele irelevante) si am investigat relatia dintre diferiti indicatori si rezultatul final. Cateva concluzii importante:

* **Matricea de Corelatie (Heatmap):** Analiza arata ca cei mai puternici predictori pentru o victorie a echipei Blue sunt Aurul Total (corelatie de 0.45) si Experienta Totala (0.44). Numarul de kill-uri este important (0.39), dar economia dicteaza ritmul jocului. 
* **Impactul Aurului (Boxplot):** Distributia aurului ne arata clar ca, in meciurile castigate, echipa Blue reuseste sa stranga o cantitate mediana de aur semnificativ mai mare in primele 10 minute.

---

## 4. Implementare: Feature Engineering si Importanta Caracteristicilor

Modelele de Machine Learning reusesc sa extraga informatii mult mai valoroase din avantaje relative decat din valori absolute. De exemplu, faptul ca o echipa a acumulat 15.000 de monede de aur la minutul 10 nu ofera o imagine completa daca nu cunoastem si economia echipei adverse. 

Prin urmare, am aplicat tehnici de **Feature Engineering** pentru a crea variabile noi care sa reflecte dominanta pe harta:
* Am calculat diferentele directe intre cele doua echipe: `goldDiff` (Diferenta de Aur), `xpDiff` (Diferenta de Experienta), `killsDiff` (Diferenta de Eliminari) si `minionsDiff` (Diferenta de minioni ucisi).
* Aceste variabile sintetice au redus "zgomotul" din date si au ajutat modelele sa inteleaga mai bine conceptul de *snowball*.

Pentru a stabili o ierarhie clara a acestor variabile, am antrenat un model preliminar de Random Forest si am extras **Feature Importance** (Importanta Caracteristicilor). Rezultatele au demonstrat fara echivoc ca `goldDiff` si `xpDiff` sunt pilonii principali care influenteaza decizia de victorie. Controlul hartii (ward-urile) si obiectivele neutre individuale au aratat o importanta secundara in fata puterii de cumparare si a nivelului campionilor.

---

## 5. Aspecte Teoretice si Algoritmi Utilizati

Deoarece predictia victoriei este o problema clasica de clasificare binara, am ales sa testam, sa optimizam si sa comparam trei arhitecturi fundamentale din invatarea automata, fiecare avand o abordare matematica si structurala diferita:

1. **Regresia Logistica (Logistic Regression):** A fost utilizata drept model de referinta (baseline). Este un algoritm liniar care estimeaza probabilitatea ca o instanta sa apartina unei anumite clase utilizand o functie logistica (sigmoid). Desi este rapida si usor de interpretat matematic, are limitari in a capta relatii non-liniare complexe intre variabilele jocului.

2. **Random Forest Classifier:** Reprezinta un algoritm de tip *Ensemble* bazat pe tehnica *Bagging* (Bootstrap Aggregating). Acesta construieste o "padure" de sute de arbori de decizie independenti. Fiecare arbore primeste un subset aleator de date si de caracteristici, iar decizia finala este luata printr-un "vot majoritar". Acest algoritm este recunoscut pentru robustetea sa si rezistenta la overfitting (suprapotrivire) in prezenta zgomotului din date.

3. **Gradient Boosting Classifier:** Spre deosebire de Random Forest, care construieste arborii in paralel, Gradient Boosting foloseste tehnica *Boosting*, construind arborii in mod secvential. Fiecare arbore nou este antrenat in mod specific pentru a corecta erorile (reziduurile) facute de ansamblul arborilor anteriori. Este unul dintre cei mai performanti algoritmi pentru date tabelare, fiind capabil sa descopere tipare subtile, cu pretul unui timp de antrenare mai ridicat.

---

## 6. Antrenarea, Evaluarea si Optimizarea Modelelor

Setul de date a fost partitionat utilizand functia `train_test_split`, pastrand 80% din meciuri pentru antrenarea modelelor si 20% ca set de testare "nevazut" pentru o evaluare corecta.

### Metrici de Evaluare
Nu ne-am limitat la o simpla raportare a acuratetii. Performanta a fost disecata folosind:
* **Accuracy (Acuratetea):** Procentul total de predictii corecte (atat victorii, cat si infrangeri nimerite).
* **Precision (Precizia):** Dintre toate meciurile in care modelul a prezis victoria echipei Blue, in cate a avut dreptate? Penalizeaza "alarmele false".
* **Recall (Sensibilitatea):** Dintre toate victoriile reale ale echipei Blue, cate a gasit modelul? Penalizeaza predictiile ratate.
* **F1-Score:** Media armonica intre Precizie si Recall, oferind cea mai clara imagine asupra stabilitatii algoritmului.

### Optimizarea Hiperparametrilor (GridSearchCV)
Pentru a maximiza capacitatea de generalizare si a preveni overfitting-ul pe datele de antrenament, am supus algoritmii bazati pe arbori la un proces riguros de "Hyperparameter Tuning" (Optimizarea Hiperparametrilor). 

Am utilizat functia `GridSearchCV` combinata cu Validarea Incrucisata (K-Fold Cross-Validation, cu K=3). Prin acest proces, algoritmul a testat iterativ zeci de combinatii de setari. De exemplu, in cazul Random Forest si Gradient Boosting, am restrictionat `max_depth` (adancimea maxima a arborilor) si am impus un prag `min_samples_split` (numarul minim de exemple necesar pentru o ramificare). Prin "taierea" (pruning) arborilor prea complecsi, modelele optimizate au invatat logica generala a jocului, refuzand sa memoreze exceptiile sau situatiile absurde.

---

## 7. Explainable AI (XAI) - Interpretarea Deciziilor

Cel mai mare dezavantaj al modelelor de Machine Learning complexe (cunoscute sub denumirea de "Black Box" / Cutii Negre) este lipsa de transparenta: desi ofera predictii extrem de precise, nu pot explica *de ce* au ajuns la acea concluzie.

Pentru a rezolva aceasta problema si a intelege deciziile modelelor noastre, am integrat concepte de **Explainable AI (XAI)** utilizand libraria **SHAP (SHapley Additive exPlanations)**. Bazata pe teoria jocurilor de coalitie (Cooperative Game Theory), metoda SHAP calculeaza contributia marginala exacta a fiecarei variabile la predictia finala a unui meci specific.

Generand grafice de tip *Waterfall* (Cascada) pentru meciuri de test, am putut observa direct "in mintea" algoritmului. SHAP ne-a aratat clar cum o diferenta de +2000 de Aur (`goldDiff`) ofera un impuls masiv de probabilitate spre victorie, in timp ce factori adiacenti (cum ar fi lipsa dragonilor sau a garzilor de viziune) actioneaza ca penalizari procentuale care trag predictia in directia opusa. Aceasta transparenta transforma modelul dintr-un simplu calculator de probabilitati intr-un instrument analitic de o valoare inestimabila pentru intelegerea dinamicii jocului de League of Legends.

---

## 8. Concluzii si Dezvoltari Viitoare

Am demonstrat ca fenomenul de "snowball" din League of Legends este masurabil si predictibil inca de la minutul 10. Modelele de invatare automata au reusit sa atinga o acuratete stabila de aproximativ 75%. Principalul predictor al victoriei este diferenta economica si de experienta. Pe viitor, proiectul ar putea fi extins prin adaugarea datelor specifice despre campionii alesi (team composition) pentru o precizie si mai mare.
