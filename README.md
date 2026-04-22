# Predictia rezultatului meciurilor de League of Legends la minutul 10

**Autor:** Babut Stefan Andrei  


## Detalii Tehnice si Sursa Datelor
* **Dataset:** League of Legends SoloQ matches at 10 minutes (2024)
* **Link:** [Kaggle Dataset Link](https://www.kaggle.com/datasets/karlorusovan/league-of-legends-soloq-matches-at-10-minutes-2024)
* **Data descarcarii:** 26 Martie 2026
* **Tehnologii:** Python 3, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`

---

## 1. Introducere si motivatia alegerii bazei de date

Domeniul jocurilor video a evoluat masiv in ultimul deceniu, trecand de la o simpla sursa de divertisment la o adevarata industrie competitiva, cunoscuta sub numele de e-sports. Unul dintre cele mai populare titluri la nivel global este League of Legends (LoL), un joc de tip MOBA (Multiplayer Online Battle Arena) in care doua echipe a cate cinci jucatori se confrunta pentru a distruge baza adversa. 

Motivatia din spatele alegerii acestei teme si a bazei de date aferente este complexitatea si volumul urias de date pe care un meci de League of Legends le genereaza in timp real. Jocurile de tip MOBA sunt cunoscute pentru un fenomen numit "snowball effect" (efectul de bulgare de zapada). Acest concept sugereaza ca un avantaj minor obtinut in primele minute ale jocului (cum ar fi eliminarea unui adversar sau distrugerea unui obiectiv) se acumuleaza exponential, oferind echipei respective mai mult aur si experienta, ceea ce face ca victoria sa fie din ce in ce mai probabila.

Proiectul de fata isi propune sa testeze aceasta ipoteza empiric, folosind algoritmi de Invatare Automata. Am ales sa analizez daca statisticile acumulate in primele 10 minute de joc sunt suficiente pentru a stabili cu exactitate ce echipa va castiga meciul la final, indiferent de durata totala a acestuia. Baza de date aleasa este perfect pentru o problema de clasificare binara, oferind un mediu ideal pentru aplicarea, optimizarea si compararea unor modele de predictie complexe.

---

## 2. Contextul sursei de date si cerintele proiectului

Pentru realizarea acestui proiect, datele au fost preluate de pe platforma Kaggle, recunoscuta pentru furnizarea de seturi de date calitative. Baza de date aleasa contine extrageri din API-ul oficial al jocului pentru mii de meciuri jucate la un nivel inalt de competitivitate (clasamentul SoloQ).

Setul de date brut a continut initial un numar de peste 24.200 de meciuri distincte. Pentru fiecare meci in parte, au fost inregistrate atat victoria finala (eticheta noastra de predictie), cat si zeci de variabile inregistrate fix la secunda in care ceasul jocului a atins minutul 10:00. Aceste variabile includ elemente economice (aurul total), elemente de experienta, obiective neutre distruse (dragoni si heralzi) si elemente de control al hartii (wards).

### Ce dorim sa obtinem:
Obiectivul principal al proiectului este implementarea si compararea a trei algoritmi distincti de invatare automata pentru a clasifica si prezice echipa castigatoare (Blue Team sau Red Team). 

Pe langa acuratetea predictiei, ne dorim sa raspundem la urmatoarele intrebari:
1. Care este cel mai puternic factor determinant al victoriei in faza de inceput a jocului?
2. Este avantajul economic (aurul) mai important decat controlul hartii (viziunea si dragonii)?
3. Cat de precis poate un model matematic sa prevada rezultatul unui meci in care factorul uman inca poate produce rasturnari de situatie?

---

## 3. Implementare: Analiza Exploratorie a Datelor (EDA)

In faza de analiza exploratorie, am curatat datele (eliminand valorile nule si coloanele irelevante) si am investigat relatia dintre diferiti indicatori si rezultatul final. Cateva concluzii importante:

* **Heatmap:** Analiza arata ca cei mai puternici predictori pentru o victorie a echipei Blue sunt Aurul Total (corelatie de 0.45) si Experienta Totala (0.44). Numarul de kill-uri este important (0.39), dar economia si experienta dicteaza ritmul jocului. 
* **Impactul Aurului (Boxplot):** Distributia aurului ne arata clar ca, in meciurile castigate, echipa Blue reuseste sa stranga o cantitate mediana de aur semnificativ mai mare in primele 10 minute.

---

## 4. Implementare: Feature Engineering si Importanta Caracteristicilor

Deoarece modelele de Machine Learning extrag mai multe informatii din avantaje relative decat din valori absolute, am aplicat tehnici de Feature Engineering:
* Am calculat diferentele directe intre cele doua echipe la minutul 10 (`goldDiff`, `xpDiff`, `killsDiff`, `minionsDiff`).
* Pentru a stabili o ierarhie clara, am antrenat un model de Random Forest si am extras importanta caracteristicilor (Feature Importance). Rezultatele au confirmat ca diferenta de aur (`goldDiff`) si diferenta de experienta (`xpDiff`) sunt pilonii principali care influenteaza predictia, contand mult mai mult decat obiectivele minore.
