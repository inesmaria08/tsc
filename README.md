# InkTime Smartwatch v5 - Documentație Hardware (DVT)

Acest repository conține pachetul complet de proiectare hardware (schematică, layout PCB, fișiere de fabricație) pentru **InkTime**, un smartwatch ultra-low-power. Proiectul este axat pe eficientizarea consumului energetic și integrarea unui afișaj bi-stabil, eliminând limitările de autonomie specifice dispozitivelor portabile convenționale.

---

## 1. Subsisteme Hardware Integrate

Arhitectura sistemului a fost segmentată în blocuri funcționale clare, utilizând componente specializate pentru a minimiza consumul de energie în modul stand-by (parasitic draw).

### 1.1. Microcontroller și RF
Implementarea are la bază SoC-ul **Nordic Semiconductor nRF52840**, selectat pentru eficiența superioară în gestionarea stivei Bluetooth 5.0 (BLE). Secțiunea RF integrează o antenă ceramică SMD de 2.45GHz (Johanson 2450AT18). Pentru a menține integritatea semnalului radio și a asigura o propagare optimă, zona de sub antenă a fost tratată ca o zonă strictă de "Keep-out", fiind complet izolată de planurile de cupru pe toate straturile PCB-ului.

### 1.2. Power Management Unit (PMIC)
Pentru a garanta o funcționare stabilă a display-ului e-Paper, sensibil la fluctuațiile de tensiune specifice descărcării acumulatorului Li-Po, arhitectura de alimentare exclude regulatoarele LDO clasice în favoarea unor soluții cu randament ridicat:
* **RT6160A:** Regulator Buck-Boost de înaltă eficiență, capabil să mențină o ieșire stabilă de 3.3V pe întreg parcursul descărcării bateriei (4.2V - 3.0V).
* **BQ25180:** Circuit integrat de încărcare liniară, conectat direct la portul USB-C (16-pin) și protejat împotriva descărcărilor electrostatice (ESD) de cipul USBLC6-2SC6Y.
* **MAX17048:** Fuel Gauge care implementează algoritmul ModelGauge pentru monitorizarea precisă a stării de încărcare a bateriei (State of Charge), eliminând necesitatea unui rezistor de shunt și comunicând via I2C.

### 1.3. Interfață Utilizator și Senzori
* **Display e-Paper:** Conectat prin intermediul magistralei SPI (conector Molex FPC cu 24 de pini). Datorită tehnologiei bi-stabile, consumul de curent pentru reținerea imaginii este nul.
* **IMU (Inertial Measurement Unit):** Accelerometrul triaxial ultra-low-power Bosch BMA423 este utilizat pentru detectarea mișcării și activarea sistemului (tilt-to-wake), comunicând prin I2C și semnale de întrerupere hardware.
* **Feedback Haptic:** Driver-ul DRV2605 (Texas Instruments) controlează motorul de vibrație ERM, gestionând efecte haptice pre-procesate fără a încărca suplimentar procesorul principal.
* **Input Hardware:** Trei butoane tactile SMD conectate direct la porturile GPIO (cu rezistențe pull-up interne) pentru navigarea în meniul dispozitivului.

---

## 2. Considerente de Producție (PCBA) și Reguli DFM

Designul a fost optimizat riguros pentru a îndeplini cerințele fabricării la scară (Design for Manufacturing) și pentru a asigura o integrare mecanică perfectă în carcasa smartwatch-ului:

1. **Optimizare PCBA:** Toate componentele SMD sunt plasate exclusiv pe stratul superior (TOP Layer). Această abordare reduce masiv complexitatea, timpul și costurile procesului automatizat Pick & Place. Placa de bază a fost proiectată pentru o grosime a substratului de 1.0mm, vitală pentru menținerea unui profil subțire (low-profile) al ansamblului final.
2. **Reguli de Rutare și Putere:** Traseele de alimentare (VCC, VBUS, 3V3) au fost rutate cu o lățime sigură de minimum 0.3mm pentru a suporta vârfurile de consum (în special în timpul refresh-ului e-Paper și activării motorului haptic). Magistralele de semnal de mare viteză utilizează o lățime de 0.15mm.
3. **Integritate RF și Planuri de Masă:** S-au implementat planuri de masă continue (GND) atât pe stratul TOP, cât și pe BOTTOM. S-a utilizat tehnica de "via stitching" manual, cu o densitate crescută în proximitatea circuitului radio și a convertorului DC/DC, pentru a oferi o cale de retur de impedanță minimă și a reduce emisiile EMI.
