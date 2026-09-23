# Analiza transakcija i prijevara

Projekt analize financijskih transakcija i potencijalnih prijevara izrađen u sklopu kolegija **Modeliranje i primjena skladišta podataka** na Fakultetu organizacije i informatike Sveučilišta u Zagrebu.

Cilj projekta je izgraditi funkcionalno skladište podataka koje omogućuje analizu transakcija, korisnika, kartica, trgovaca, lokacija i vremena te kroz interaktivne Tableau vizualizacije prikazati obrasce potrošnje i potencijalno rizične aktivnosti.

---

## Pregled projekta

Projekt prikazuje kompletan proces od pripreme izvornog skupa podataka do izrade analitičkih izvještaja:

**CSV podaci → Excel → ETL → Access → Star Schema → Tableau → Dashboard aplikacija**

U projektu je izrađeno dimenzijsko skladište podataka temeljeno na **star schema** modelu. Središte modela čini činjenična tablica `Transakcija`, povezana s dimenzijama klijenta, kartice, lokacije, trgovca, datuma i vremena.

Projekt obuhvaća:

* pripremu i čišćenje podataka
* ETL proces
* transformaciju podataka pomoću SQL upita
* modeliranje skladišta podataka
* izradu dimenzijskih i činjenične tablice
* povezivanje podataka u Tableauu
* izradu analitičkih izvještaja
* analizu potencijalnih prijevara
* izradu interaktivne dashboard aplikacije

---

## Korištene tehnologije

| Tehnologija          | Uloga                                                          |
| -------------------- | -------------------------------------------------------------- |
| **Microsoft Excel**  | Početna obrada i priprema CSV podataka                         |
| **Microsoft Access** | Izrada skladišta podataka, SQL transformacije i model zvijezde |
| **SQL**              | Čišćenje, transformacija i učitavanje podataka                 |
| **Tableau**          | Vizualizacija, analiza i izrada interaktivnih dashboarda       |

---

## Dataset

Projekt koristi skup podataka:

**Financial Transactions Dataset: Analytics**

Dataset sadrži podatke o:

* financijskim transakcijama
* korisnicima
* karticama
* trgovcima
* lokacijama
* vremenu transakcija
* oznakama potencijalnih prijevara

Izvorni skup podataka sastoji se od tri CSV datoteke:

```text
transactions_data.csv
cards_data.csv
users_data.csv
```

Izvorni `transactions_data.csv` sadržavao je približno **2 milijuna zapisa**, dok su `cards_data.csv` i `users_data.csv` sadržavali približno 6.000 odnosno 2.000 zapisa. Za potrebe projekta skupovi su reducirani na 2.000 redaka radi lakše obrade i izrade vizualizacija.


---

## ETL proces

ETL proces podijeljen je u tri glavne faze:

### 1. Extract

Podaci su preuzeti iz tri CSV datoteke i početno obrađeni u Excelu.

Tijekom ekstrakcije:

* učitani su CSV podaci
* definirani su separatori stupaca
* prilagođena su imena atributa
* provjereni su tipovi podataka
* određene tekstualne vrijednosti pretvorene su u numeričke vrijednosti

Primjer transformacije bio je uklanjanje znaka `$` iz financijskih atributa te pretvaranje vrijednosti u numerički format.

### 2. Transform

Nakon učitavanja podataka u Access provedene su različite transformacije pomoću SQL upita.

Transformacije uključuju:

* standardizaciju naziva i vrijednosti
* prevođenje pojedinih kategorija na hrvatski jezik
* pripremu podataka za dimenzijske tablice
* uklanjanje duplikata
* pripremu podataka za činjeničnu tablicu

Primjer:

```sql
UPDATE cards_data
SET tip_kartice = 'Kreditna'
WHERE tip_kartice = 'Credit';
```

### 3. Load

Nakon transformacije podaci su učitani u dimenzijske i činjeničnu tablicu.

Konačno skladište podataka povezano je s Tableauom, gdje su izrađeni izvještaji i interaktivna dashboard aplikacija.

---

## Data Warehouse – Star Schema

Za modeliranje skladišta podataka korišten je **Star Schema** pristup.

Model se sastoji od:

### Dimenzijskih tablica

* `Klijent`
* `Kartica`
* `Lokacija`
* `Trgovac`
* `Datum`
* `Vrijeme`

### Činjenične tablice

* `Transakcija`

Struktura modela omogućuje analizu transakcija prema različitim dimenzijama poput korisnika, kartice, lokacije, trgovca i vremena.

### Ključne mjere

U činjeničnoj tablici koriste se podaci poput:

* iznosa transakcije
* MCC koda
* vrste transakcije
* oznake prijevare
* podataka o vremenu i lokaciji transakcije

---

## SQL i modeliranje

Primjer strukture činjenične tablice:

```sql
CREATE TABLE Transakcija (
    transakcija_id INT PRIMARY KEY,
    kartica_id INT,
    klijent_id INT,
    lokacija_id INT,
    datum_id INT,
    vrijeme_id INT,
    trgovac_id INT,
    MCC CHAR(4),
    vrsta_transakcije VARCHAR(20),
    iznos INT,
    oznaka_prijevare VARCHAR(2)
);
```

Tablica `Transakcija` povezuje se s dimenzijama pomoću primarnih i vanjskih ključeva.

---

## Analize i vizualizacije

U Tableauu je izrađeno više interaktivnih izvještaja.

### 1. Analiza kartičnih brendova

Analizira se ukupna vrijednost transakcija prema:

* kanalu plaćanja
* brendu kartice

Obuhvaćeni su brendovi:

* Visa
* Mastercard
* Amex
* Discover

---

### 2. Promet prema dobnim skupinama

Analiza prikazuje:

* ukupni promet prema dobnim skupinama
* korištenje različitih tipova kartica
* usporedbu debitnih, prepaid i kreditnih kartica

Dobne skupine izrađene su pomoću binova širine 5 godina.

---

### 3. Gustoća transakcija po lokaciji

Analizira se broj transakcija prema:

* saveznoj državi
* gradu

Vizualizacija omogućuje detaljniju analizu geografskih područja s većim volumenom transakcija.

---

### 4. Aktivnost prema danima u tjednu

Analiza prikazuje promjene u ukupnom iznosu transakcija tijekom dana u tjednu.

Dodatno je korištena linearna linija trenda za prikaz općeg smjera kretanja prometa.

---

### 5. Korištenje kartica prema zaduženosti

Izrađeni su Tableau calculated fields:

```text
DTI_klijenta
Promet_po_kartici
Risk_segment
```

`Risk_segment` kombinira pokazatelje zaduženosti i korištenja kartica te korisnike razvrstava u definirane kategorije rizika. Pragovi za segmentaciju određeni su na temelju distribucije podataka i procjene u sklopu projekta.

---

### 6. Raspodjela iznosa transakcija

Analizira se distribucija iznosa transakcija.

Korišten je parametar:

```text
Prag_visokog_iznosa
```

koji omogućuje interaktivno definiranje granice između visokih i niskih iznosa transakcija.

---

### 7. Geografska raspodjela

Karta prikazuje:

* gradove
* broj transakcija
* ukupnu vrijednost transakcija

Veličina oznake predstavlja ukupni iznos transakcija, dok dodatna vizualna oznaka prikazuje broj transakcija.

---

## Fraud Analysis

Poseban dio projekta posvećen je analizi transakcija označenih kao prijevarne.

Analiziraju se:

* lokacije
* gradovi
* države
* MCC kategorije
* sati u danu
* segmenti korisnika

Jedan od izvještaja koristi `oznaka_prijevare = True` kako bi se analizirale samo transakcije označene kao prijevarne.

Dodatna analiza prikazuje broj prijevarnih transakcija prema danu i satu te omogućuje filtriranje prema prethodno definiranim `Risk_segment` kategorijama.

> Rezultate analize treba promatrati kao analitičke obrasce unutar korištenog dataseta, a ne kao stvarni sustav za automatsko otkrivanje financijskih prijevara.

---

## Tableau Dashboard

Svi izrađeni izvještaji objedinjeni su u interaktivnu Tableau aplikaciju.

Aplikacija omogućuje:

* navigaciju između izvještaja
* filtriranje podataka
* usporedbu različitih segmenata
* pregled analiza na jednom mjestu
* povratak na početni dashboard

Za svaki izvještaj izrađen je zaseban dashboard, a navigacija između dashboarda ostvarena je pomoću Tableau navigacijskih elemenata.

---

## Projektna struktura

Preporučena struktura repozitorija:

```text
analiza-transakcija-i-prijevara/
│
├── README.md
│
├── data/
│   └── README.md
│
├── sql/
│   ├── create_dimensions.sql
│   ├── transformations.sql
│   ├── create_fact_table.sql
│   └── load_data.sql
│
├── tableau/
│   ├── dashboards/
│   └── screenshots/
│
├── docs/
│   ├── project-report.pdf
│   └── star-schema.png
│
└── .gitignore
```

> Datoteke i strukturu repozitorija potrebno je prilagoditi stvarnim datotekama koje se objavljuju na GitHubu.

---

## Ključni rezultati projekta

Projekt demonstrira praktičnu primjenu:

* **Data Warehousing**
* **ETL procesa**
* **SQL-a**
* **Star Schema modeliranja**
* **Data Visualization**
* **Business Intelligence**
* **Tableau calculated fields**
* **Fraud Analytics**
* **Geographic Analytics**

Krajnji rezultat je funkcionalna analitička aplikacija koja objedinjuje skladište podataka i više Tableau izvještaja za istraživanje transakcijskih obrazaca i potencijalno rizičnih aktivnosti.

---

## Screenshots

1. Model zvijezde
<img width="745" height="446" alt="Slika zaslona 2026-09-23 u 11 38 13" src="https://github.com/user-attachments/assets/5a86a490-0fad-4c4d-bfe5-11db81cbef2b" />

2. Dashboard
<img width="806" height="410" alt="Slika zaslona 2026-09-23 u 11 39 26" src="https://github.com/user-attachments/assets/2ecdf97b-9a21-40d8-a7b0-784e651dea5a" />

3. Pregled najaktivnih dana u tjednu prema iznosu transakcija
<img width="729" height="389" alt="Slika zaslona 2026-09-23 u 11 40 15" src="https://github.com/user-attachments/assets/fbbecff2-bda5-4145-b999-8333bcf54180" />

4. Analizi tržišnog udjela i preferenciji kartičnih brendova
<img width="807" height="408" alt="Slika zaslona 2026-09-23 u 11 41 10" src="https://github.com/user-attachments/assets/8c0ce5ee-ed6a-411e-9f9a-632cd5f3d540" />

5. Promet po dobnim skupinama
<img width="847" height="420" alt="Slika zaslona 2026-09-23 u 11 41 59" src="https://github.com/user-attachments/assets/9bcb8ff5-5527-4644-befe-fcbde2114d81" />

6. Korišenje kartica zaduženosti klijenta
<img width="871" height="428" alt="Slika zaslona 2026-09-23 u 11 42 53" src="https://github.com/user-attachments/assets/94caeb73-49ac-4cfd-b64f-ce850a27da6c" />




---

## Izvor podataka

**Financial Transactions Dataset: Analytics** – Kaggle

Izvorni dataset korišten je kao temelj za analizu transakcija, korisnika i kartica.

P
