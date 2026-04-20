# 🏠 Centralina Aerazione Domestica

> Progetto per il corso di **Architettura degli Elaboratori – Modulo Laboratorio**  
> Università degli Studi di Camerino  
> Ferrari Kevin – Matricola: 130668

---

## Descrizione

Sistema embedded di controllo qualità dell'aria per ambienti domestici, basato su **Arduino (ATmega328P)**. Monitora il livello di CO₂ e gestisce automaticamente la ventilazione scegliendo tra **recupero di calore** e **scambio diretto**, in base alle temperature rilevate.

Simulazione disponibile su Wokwi: [🔗 Apri progetto](https://wokwi.com/projects/434489889439706113)

---

## Architettura del sistema

Il sistema è progettato come un insieme di **automi indipendenti** che comunicano tramite variabili globali, secondo il paradigma bare-metal (accesso diretto ai registri, nessuna libreria Arduino).

### Moduli funzionali

| Modulo | Funzione |
|--------|----------|
| **MOD1** | Acquisizione livello CO₂ via ADC (potenziometro simulato) |
| **MOD2** | Lettura sensori DHT22 (temperatura interna/esterna) |
| **MOD3** | Logica di attivazione recupero calore + controllo relay |
| **MOD4** | Console di comando LCD 16×2 con 4 tasti (`< > S X`) |

---

## Logica di controllo

```
CO₂ > soglia_MAX (default: 1000 ppm)?
├── NO  → Sistema OFF, sleep-mode
└── SI  → Leggi temp. interna e esterna
          │
          └── temp_esterna ∈ [T_ben_min, T_ben_max]?
              ├── SI  → Scambio diretto (Relay 1 ON, Relay 2 OFF)
              └── NO  → Recupero di calore  (Relay 1 ON, Relay 2 ON)
```

### Scenari gestiti

| Scenario | T_int | T_est | Modalità |
|----------|-------|-------|----------|
| Inverno | 21°C | 2°C | Recupero di calore |
| Estate | 24°C | 34°C | Recupero di calore |
| Mezza stagione | 26°C | 22°C | Scambio diretto |
| CO₂ OK | — | — | Sistema spento |

---

## Hardware

| Componente | Quantità | Note |
|------------|----------|------|
| Arduino Uno (ATmega328P) | 1 | MCU principale |
| Sensore DHT22 | 2 | Temperatura interna ed esterna |
| Relay Module | 2 | Ventola + deviatore recupero calore |
| Potenziometro | 1 | Simulazione sensore CO₂ |
| Display LCD 16×2 | 1 | Console utente (4-bit mode) |
| Pulsanti | 4 | Navigazione menu (`< > S X`) |

### Pinout

| Pin | Dispositivo |
|-----|-------------|
| PD2 | DHT22 esterno |
| PD3 | DHT22 interno |
| PD4 | Relay ventilazione |
| PD5 | Relay recupero calore |
| PD7 | LCD RS |
| PB0–PB4 | LCD E, D4–D7 |

---

## Gestione interrupt & timer

| Interrupt | Timer | Funzione |
|-----------|-------|----------|
| Periodico | TIMER1 | Lettura sensori ogni 5 s |
| Pin Change | — | Rilevazione pressione tasti |
| Debounce | TIMER2 | Anti-rimbalzo pulsanti |
| Bit-timing | TIMER0 | Lettura protocollo DHT22 |

La MCU entra in **sleep-mode** tra un ciclo e l'altro; si risveglia esclusivamente tramite interrupt.

---

## Variabili globali principali

```c
volatile uint8_t  co2_level;               // Livello CO₂ attuale (ADC)
volatile uint8_t  system_active;           // Stato sistema (0=OFF, 1=ON)
volatile int16_t  temp_internal_x10;       // Temp. interna ×10
volatile int16_t  temp_external_x10;       // Temp. esterna ×10
volatile uint8_t  co2_threshold;           // Soglia CO₂ (default: 1000 ppm)
volatile uint8_t  temp_benessere_min;      // T comfort min (default: 20°C)
volatile uint8_t  temp_benessere_max;      // T comfort max (default: 25°C)
volatile uint8_t  read_co2_and_temp_flag;  // Flag lettura periodica (TIMER1)
```

---

## Parametri configurabili via console

- **Soglia CO₂** (default: 1000 ppm)
- **Temperatura minima di benessere** (default: 20°C)
- **Temperatura massima di benessere** (default: 25°C)

---

## Possibili evoluzioni future

- Allarme buzzer per CO₂ > soglia critica (es. 1500 ppm)
- Sfruttamento lettura umidità DHT22 per controllo anti-condensa
- Logging seriale esteso per diagnostica

---

## Vincoli progettuali

- ✅ Nessuna funzione Arduino (`digitalWrite`, `delay`, ecc.)
- ✅ Nessuna libreria esterna
- ✅ Accesso diretto ai registri del microcontrollore
- ✅ Gestione temporale interamente via ISR
- ✅ Architettura a moduli indipendenti con variabili globali condivise
