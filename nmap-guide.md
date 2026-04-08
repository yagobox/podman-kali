# Nmap — Guida ai Principali Comandi e Test

> **Avviso legale:** Utilizzare Nmap esclusivamente su reti e sistemi di cui si possiede l'autorizzazione esplicita. La scansione non autorizzata è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
nmap --version
```

### Installazione manuale
```bash
apt update && apt install -y nmap
```

---

## 2. Sintassi generale

```
nmap [tipo di scansione] [opzioni] <target>
```

Il target può essere:
- Un singolo IP: `192.168.1.1`
- Un range: `192.168.1.1-254`
- Una subnet: `192.168.1.0/24`
- Un hostname: `esempio.com`
- Un file di host: `-iL hosts.txt`

---

## 3. Scansioni di base

### Scansione semplice di un host
```bash
nmap 192.168.1.1
```

### Scansione di una subnet intera
```bash
nmap 192.168.1.0/24
```

### Scansione di più host
```bash
nmap 192.168.1.1 192.168.1.2 192.168.1.10
```

### Scansione da file di host
```bash
nmap -iL hosts.txt
```

### Escludere host dalla scansione
```bash
nmap 192.168.1.0/24 --exclude 192.168.1.1
```

---

## 4. Tipi di scansione porta

### SYN Scan (stealth, default con root)
```bash
nmap -sS 192.168.1.1
```

### TCP Connect Scan (senza privilegi root)
```bash
nmap -sT 192.168.1.1
```

### UDP Scan
```bash
nmap -sU 192.168.1.1
```

### Scansione combinata TCP + UDP
```bash
nmap -sS -sU 192.168.1.1
```

### ACK Scan (rileva firewall)
```bash
nmap -sA 192.168.1.1
```

### NULL Scan (pacchetti senza flag)
```bash
nmap -sN 192.168.1.1
```

### FIN Scan
```bash
nmap -sF 192.168.1.1
```

### Xmas Scan (flag FIN + PSH + URG)
```bash
nmap -sX 192.168.1.1
```

---

## 5. Selezione delle porte

### Scansione delle porte più comuni (top 1000, default)
```bash
nmap 192.168.1.1
```

### Porta specifica
```bash
nmap -p 80 192.168.1.1
```

### Range di porte
```bash
nmap -p 1-1000 192.168.1.1
```

### Porte specifiche multiple
```bash
nmap -p 22,80,443,8080 192.168.1.1
```

### Tutte le 65535 porte
```bash
nmap -p- 192.168.1.1
```

### Top N porte più comuni
```bash
nmap --top-ports 100 192.168.1.1
```

---

## 6. Rilevamento versioni e OS

### Rilevare versione dei servizi
```bash
nmap -sV 192.168.1.1
```

### Rilevare sistema operativo
```bash
nmap -O 192.168.1.1
```

### Intensità del rilevamento versione (0-9)
```bash
nmap -sV --version-intensity 5 192.168.1.1
```

### Scansione aggressiva (OS + versioni + script + traceroute)
```bash
nmap -A 192.168.1.1
```

---

## 7. Host Discovery (ping scan)

### Ping scan (verifica host attivi senza scansire porte)
```bash
nmap -sn 192.168.1.0/24
```

### Scansione saltando la fase di ping (assume host attivi)
```bash
nmap -Pn 192.168.1.1
```

### Usare solo ARP ping (su rete locale)
```bash
nmap -PR 192.168.1.0/24
```

---

## 8. Nmap Scripting Engine (NSE)

### Eseguire gli script di default
```bash
nmap -sC 192.168.1.1
```

### Eseguire uno script specifico
```bash
nmap --script=http-title 192.168.1.1
```

### Eseguire più script
```bash
nmap --script=http-title,http-headers 192.168.1.1
```

### Script per categoria
```bash
nmap --script=vuln 192.168.1.1       # vulnerabilità note
nmap --script=auth 192.168.1.1       # autenticazione
nmap --script=brute 192.168.1.1      # brute force
nmap --script=discovery 192.168.1.1  # discovery
nmap --script=safe 192.168.1.1       # script sicuri
```

### Script SMB (enumerazione Windows)
```bash
nmap --script=smb-enum-shares,smb-enum-users -p 445 192.168.1.1
```

### Script HTTP
```bash
nmap --script=http-enum -p 80,443 192.168.1.1
```

### Rilevare vulnerabilità con script vuln
```bash
nmap --script=vuln -sV 192.168.1.1
```

---

## 9. Output e salvataggio risultati

### Output normale su file
```bash
nmap -oN risultati.txt 192.168.1.1
```

### Output XML
```bash
nmap -oX risultati.xml 192.168.1.1
```

### Output Grepable
```bash
nmap -oG risultati.gnmap 192.168.1.1
```

### Tutti i formati contemporaneamente
```bash
nmap -oA risultati 192.168.1.1
```

---

## 10. Velocità e timing

| Template | Flag  | Descrizione                          |
|----------|-------|--------------------------------------|
| Paranoid | `-T0` | Molto lento, evasione IDS            |
| Sneaky   | `-T1` | Lento, evasione IDS                  |
| Polite   | `-T2` | Riduce carico sulla rete             |
| Normal   | `-T3` | Default                              |
| Aggressive | `-T4` | Più veloce, rete affidabile        |
| Insane   | `-T5` | Massima velocità, possibili errori   |

```bash
nmap -T4 192.168.1.1   # consigliato per lab
nmap -T1 192.168.1.1   # per evasione
```

---

## 11. Evasione firewall e IDS

### Frammentare i pacchetti
```bash
nmap -f 192.168.1.1
```

### Usare un IP decoy (per offuscare la sorgente)
```bash
nmap -D RND:10 192.168.1.1
```

### Specificare IP sorgente
```bash
nmap -S 192.168.1.200 192.168.1.1
```

### Scansione lenta per eludere IDS
```bash
nmap -T1 --scan-delay 5s 192.168.1.1
```

### Randomizzare l'ordine degli host
```bash
nmap --randomize-hosts 192.168.1.0/24
```

---

## 12. Scansioni complete per lab (esempi)

### Ricognizione iniziale completa
```bash
nmap -sS -sV -sC -O -A -T4 -p- -oA full-scan 192.168.1.1
```

### Scoprire host attivi su rete locale
```bash
nmap -sn 192.168.1.0/24 -oN host-attivi.txt
```

### Scansione web rapida
```bash
nmap -sV -p 80,443,8080,8443 --script=http-title,http-enum 192.168.1.1
```

### Scansione vulnerabilità
```bash
nmap -sV --script=vuln -T4 192.168.1.1
```

### Scansione SMB (Windows)
```bash
nmap -p 445 --script=smb-vuln-ms17-010 192.168.1.1
```
