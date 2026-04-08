# Burp Suite — Guida ai Principali Strumenti e Test

> **Avviso legale:** Burp Suite è una piattaforma per il testing della sicurezza di applicazioni web. Utilizzarla esclusivamente su applicazioni di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione e avvio

### Su Kali Linux (già incluso)
```bash
burpsuite
```

### Avvio da terminale
```bash
java -jar burpsuite_community_v*.jar
```

### Installazione manuale
```bash
apt update && apt install -y burpsuite
```

---

## 2. Configurazione del proxy nel browser

Burp Suite intercetta il traffico agendo da proxy tra browser e server.

### Impostazioni proxy di Burp (default)
- Host: `127.0.0.1`
- Porta: `8080`

### Configurare Firefox manualmente
1. Apri **Impostazioni → Rete → Impostazioni connessione**
2. Seleziona **Configurazione manuale del proxy**
3. HTTP Proxy: `127.0.0.1`, Porta: `8080`
4. Spunta **Usa questo proxy anche per HTTPS**

### Usare FoxyProxy (estensione consigliata)
1. Installa l'estensione FoxyProxy in Firefox
2. Aggiungi un profilo: IP `127.0.0.1`, porta `8080`
3. Attivalo con un clic

### Installare il certificato CA di Burp (per HTTPS)
1. Con il proxy attivo, vai su `http://burp` nel browser
2. Clicca **CA Certificate** e scarica `cacert.der`
3. In Firefox: **Impostazioni → Privacy → Certificati → Importa**

---

## 3. Proxy — Intercettazione richieste

### Avviare l'intercettazione
- Scheda **Proxy → Intercept → Intercept is ON**

### Fermare l'intercettazione
- Clicca **Intercept is ON** per disattivarla

### Navigare nella cronologia delle richieste
- Scheda **Proxy → HTTP History**

### Inviare una richiesta a un altro modulo
- Click destro su una richiesta → **Send to Repeater / Intruder / Scanner**

---

## 4. Repeater — Test manuali

Il Repeater permette di modificare e reinviare manualmente richieste HTTP.

### Workflow tipico
1. Intercetta una richiesta in **Proxy**
2. Click destro → **Send to Repeater**
3. Modifica la richiesta nel pannello sinistro
4. Clicca **Send** e analizza la risposta

### Utilizzi comuni
- Testare parametri per SQL injection, XSS, LFI/RFI
- Testare bypass di autenticazione
- Modificare cookie e header
- Testare IDOR (Insecure Direct Object Reference)

---

## 5. Intruder — Attacchi automatizzati

L'Intruder esegue attacchi automatici modificando uno o più parametri.

### Impostare i payload
1. Intercetta la richiesta da testare
2. Click destro → **Send to Intruder**
3. Nella scheda **Positions**: seleziona i parametri da attaccare con `§valore§`
4. Nella scheda **Payloads**: inserisci la wordlist

### Tipi di attacco

| Tipo         | Descrizione                                                   |
|--------------|---------------------------------------------------------------|
| **Sniper**   | Un solo payload, un parametro alla volta                      |
| **Battering Ram** | Stesso payload su tutti i parametri simultaneamente      |
| **Pitchfork** | Più payload su più parametri in parallelo (1:1)             |
| **Cluster Bomb** | Combinazione di tutti i payload su tutti i parametri     |

### Brute force login con Intruder
1. Intercetta la richiesta di login POST
2. Invia a Intruder
3. Seleziona `§username§` e `§password§` come posizioni
4. Imposta attacco **Cluster Bomb**
5. Payload 1: lista di username
6. Payload 2: lista di password
7. Avvia l'attacco e filtra per lunghezza risposta diversa

---

## 6. Scanner — Scansione automatica vulnerabilità

> Disponibile nella versione **Burp Suite Professional**.

### Avviare una scansione passiva (analisi del traffico già catturato)
- Vai su **Dashboard → New Scan → Crawl and Audit**
- Inserisci l'URL target
- Seleziona le opzioni e avvia

### Scansione attiva di un URL specifico
1. Click destro su una richiesta in **Proxy History**
2. Seleziona **Scan** → scegli il tipo di scansione

### Scansione passiva automatica
- Già attiva di default: analizza tutto il traffico che passa per il proxy
- Risultati visibili in **Dashboard → Issue Activity**

---

## 7. Spider / Crawler — Mappatura dell'applicazione

### Avviare il crawl
1. Click destro su un host nel **Site Map**
2. Seleziona **Spider this host** (Community) o **Crawl** (Pro)

### Visualizzare la mappa del sito
- Scheda **Target → Site Map**
- Expand il dominio per vedere tutte le URL scoperte

---

## 8. Decoder — Codifica e decodifica

Strumento utile per codificare/decodificare valori in vari formati.

### Aprire il Decoder
- Scheda **Decoder**

### Formati supportati
```
URL encode / decode
HTML encode / decode
Base64 encode / decode
Hex encode / decode
ASCII hex
Gzip
SHA1 / SHA256 / MD5 (hash)
```

### Esempio di utilizzo
1. Incolla un valore codificato (es. `YWRtaW4=`)
2. Clicca **Decode as Base64**
3. Risultato: `admin`

---

## 9. Comparer — Confronto risposte

Utile per confrontare risposte HTTP e individuare differenze.

### Workflow
1. In Proxy History, click destro su due richieste → **Send to Comparer**
2. Vai in **Comparer** e clicca **Words** o **Bytes**

---

## 10. Sequencer — Analisi casualità token

Analizza la casualità di token di sessione, CSRF token, ecc.

### Workflow
1. Intercetta una risposta con un token di sessione
2. Click destro → **Send to Sequencer**
3. Imposta la posizione del token
4. Avvia la raccolta e l'analisi

---

## 11. Estensioni utili (BApp Store)

| Estensione         | Utilizzo                                          |
|--------------------|---------------------------------------------------|
| **AuthMatrix**     | Test autorizzazioni e ruoli                       |
| **JWT Editor**     | Modifica e attacca token JWT                      |
| **Param Miner**    | Scopre parametri nascosti                         |
| **ActiveScan++**   | Potenzia lo scanner attivo                        |
| **Turbo Intruder** | Intruder ad alta velocità (scriptabile)           |
| **Logger++**       | Log avanzato di tutte le richieste                |
| **Autorize**       | Test automatico di vulnerabilità IDOR             |
| **Retire.js**      | Rileva librerie JS vulnerabili                    |

### Installare un'estensione
1. Vai su **Extensions → BApp Store**
2. Cerca l'estensione e clicca **Install**

---

## 12. Shortcut da tastiera utili

| Azione                            | Shortcut                  |
|-----------------------------------|---------------------------|
| Invia a Repeater                  | `Ctrl + R`                |
| Invia a Intruder                  | `Ctrl + I`                |
| Forward richiesta intercettata    | `Ctrl + F`                |
| Toggle intercettazione            | `Ctrl + T`                |
| Nuova scheda Repeater             | `Ctrl + Shift + N`        |
| Cerca nel testo                   | `Ctrl + F` (nel pannello) |

---

## 13. Workflow completo per test web

```
1. Configura il proxy nel browser
2. Installa il certificato CA di Burp
3. Naviga sull'applicazione target → tutto il traffico viene catturato
4. Analizza le richieste in Proxy → HTTP History
5. Identifica i parametri interessanti (login, ricerca, ID...)
6. Invia le richieste sospette al Repeater per test manuali
7. Usa l'Intruder per brute force / fuzzing automatico
8. Controlla i risultati dello Scanner per vulnerabilità note
9. Usa il Decoder per analizzare valori codificati
10. Documenta le vulnerabilità trovate
```
