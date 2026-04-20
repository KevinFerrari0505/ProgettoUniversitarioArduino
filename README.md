# ProgettoUniversitarioArduino
🏠 Centralina di Aerazione Domestica

Sistema embedded per il controllo intelligente della qualità dell’aria in ambienti domestici, basato su microcontrollore e progettato secondo un approccio bare-metal (senza librerie Arduino).

📌 Descrizione

Questo progetto implementa una centralina di ventilazione capace di:

Monitorare il livello di CO₂
Attivare automaticamente la ventilazione
Scegliere la modalità più efficiente tra:
Recupero di calore
Scambio diretto con l’esterno
Minimizzare il consumo energetico tramite sleep mode e interrupt

👉 Il sistema è progettato come insieme di automi indipendenti che comunicano tramite variabili globali.

⚙️ Funzionalità principali
🌫️ Controllo CO₂
Lettura continua del livello di CO₂ (simulato con potenziometro)
Attivazione ventilazione sopra soglia configurabile (default: 1000 ppm)
🌡️ Gestione temperatura
Due sensori:
Temperatura interna
Temperatura esterna
Decisione automatica:
Recupero di calore → se temperatura esterna fuori comfort
Scambio diretto → se temperatura esterna dentro comfort
⚡ Efficienza energetica
Modalità sleep-mode
Risveglio tramite interrupt
Controlli periodici ogni 5 secondi
🎛️ Console di comando
Display LCD 16x2
4 pulsanti (< > S X)
Configurazione:
Soglia CO₂
Range temperatura di benessere
🧠 Architettura del sistema

Il sistema è suddiviso in moduli indipendenti:

🔹 MOD1 – Controllo CO₂
Lettura ADC
Confronto con soglia
Output: attivazione sistema
🔹 MOD2 – Sensori DHT22
Lettura temperatura interna/esterna
Comunicazione a basso livello (bit timing)
🔹 MOD3 – Recupero di calore
Decisione logica
Controllo relè
🔹 MOD4 – Interfaccia utente
Gestione menu
Modifica parametri
🔁 Logica di funzionamento
Lettura CO₂
Confronto con soglia
Se superata:
Attiva ventilazione
Valuta modalità (recupero o diretto)
Attesa (timer interrupt)
Ripeti ciclo
🔌 Hardware utilizzato
Microcontrollore (AVR)
Sensori:
DHT22 (temperatura)
Potenziometro (CO₂ simulata)
Relè:
Ventilazione
Recupero calore
Display LCD 16x2
Pulsanti input
🧵 Gestione interrupt
⏱️ Timer periodico → lettura sensori (ogni 5s)
🔘 Pin Change → input utente
⌛ Timer debounce → gestione pulsanti
🧾 Struttura del codice
Variabili globali

Permettono la comunicazione tra moduli:

volatile uint8_t co2_level;
volatile uint8_t system_active;
volatile int16_t temp_internal_x10;
volatile int16_t temp_external_x10;
Funzioni principali
setup()
Inizializzazione hardware
Configurazione timer
Attivazione interrupt
loop()
Gestione eventi
Logica sistema
Aggiornamento display
🚀 Simulazione

Puoi provare il progetto su Wokwi:

👉 https://wokwi.com/projects/434489889439706113

⚖️ Scelte progettuali
✔️ Approccio bare-metal
Accesso diretto ai registri
Massima efficienza
Controllo totale sull’hardware
❌ Librerie Arduino
Più semplici ma:
Più lente
Più pesanti
Meno controllabili
🔮 Possibili miglioramenti
🔔 Allarme per CO₂ elevata (buzzer)
💧 Controllo umidità (già disponibile nei DHT22)
📊 Logging dati ambientali
🌐 Integrazione IoT
👨‍💻 Autore

Kevin Ferrari
Università degli Studi di Camerino
Corso di Informatica

📚 Note

Questo progetto è stato sviluppato per il corso di:

Architettura degli Elaboratori – Modulo Laboratorio
