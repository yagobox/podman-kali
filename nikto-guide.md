# Nikto — Guida ai Principali Comandi e Test

> **Avviso legale:** Nikto è uno scanner di vulnerabilità per server web. Utilizzarlo esclusivamente su server di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
nikto -Version
```

### Installazione manuale
```bash
apt update && apt install -y nikto
```

---

## 2. Sintassi generale

```
nikto -h <host> [opzioni]
```

---

## 3. Scansioni di base

### Scansione di un host (porta 80 default)
```bash
nikto -h 192.168.1.1
```

### Scansione con hostname
```bash
nikto -h http://target.com
```

### Scansione HTTPS
```bash
nikto -h https://target.com
```

### Specificare la porta
```bash
nikto -h 192.168.1.1 -p 8080
```

### Scansione su più porte
```bash
nikto -h 192.168.1.1 -p 80,443,8080,8443
```

---

## 4. Autenticazione

### Autenticazione HTTP Basic
```bash
nikto -h http://target.com -id admin:password
```

### Usare un cookie di sessione
```bash
nikto -h http://target.com -c "PHPSESSID=abc123; security=low"
```

---

## 5. Proxy

### Usare Burp Suite come proxy
```bash
nikto -h http://target.com -useproxy http://127.0.0.1:8080
```

### Usare Tor
```bash
nikto -h http://target.com -useproxy socks5://127.0.0.1:9050
```

---

## 6. Opzioni di scansione avanzate

### Scansione con tutti i plugin
```bash
nikto -h http://target.com -Plugins @@ALL
```

### Elencare i plugin disponibili
```bash
nikto -list-plugins
```

### Usare un plugin specifico
```bash
nikto -h http://target.com -Plugins headers
nikto -h http://target.com -Plugins outdated
nikto -h http://target.com -Plugins cgi
```

### Scansione con tutte le tecniche di evasione IDS
```bash
nikto -h http://target.com -evasion 1234567
```

| Codice | Tecnica evasione                          |
|--------|-------------------------------------------|
| `1`    | Random URL encoding (non-UTF8)            |
| `2`    | Directory auto-reference (`/./`)          |
| `3`    | Premature URL ending                      |
| `4`    | Prepend long random string                |
| `5`    | Fake parameter                            |
| `6`    | TAB come separatore                       |
| `7`    | Case sensitivity URL                      |
| `8`    | Windows path separator `\`               |

---

## 7. Tuning della scansione

### Specificare le categorie di test con `-Tuning`
```bash
nikto -h http://target.com -Tuning x
```

| Codice | Categoria                            |
|--------|--------------------------------------|
| `0`    | File upload                          |
| `1`    | Interesting file / Seen in logs      |
| `2`    | Misconfiguration / Default file      |
| `3`    | Information disclosure               |
| `4`    | Injection (XSS/Script/HTML)          |
| `5`    | Remote file retrieval (inside webroot) |
| `6`    | Denial of Service                    |
| `7`    | Remote file retrieval (server wide)  |
| `8`    | Command execution / Remote shell     |
| `9`    | SQL injection                        |
| `a`    | Authentication bypass                |
| `b`    | Software identification              |
| `c`    | Remote source inclusion              |
| `x`    | Reverse tuning (esclude il codice)   |

### Esempio: solo SQL injection e XSS
```bash
nikto -h http://target.com -Tuning 94
```

---

## 8. Output e salvataggio

### Salvare in formato testo
```bash
nikto -h http://target.com -o risultati.txt -Format txt
```

### Salvare in formato HTML
```bash
nikto -h http://target.com -o risultati.html -Format html
```

### Salvare in formato CSV
```bash
nikto -h http://target.com -o risultati.csv -Format csv
```

### Salvare in formato XML
```bash
nikto -h http://target.com -o risultati.xml -Format xml
```

---

## 9. Scansione da file di host

### File con lista di host (uno per riga)
```bash
nikto -h hosts.txt
```

### File con lista di host in formato Nmap grepable
```bash
nikto -h nmap-output.gnmap
```

---

## 10. Opzioni di timing e comportamento

### Impostare il timeout di connessione (secondi)
```bash
nikto -h http://target.com -timeout 10
```

### Impostare il numero massimo di errori prima di fermarsi
```bash
nikto -h http://target.com -maxerrors 20
```

### Disabilitare il DNS lookup
```bash
nikto -h 192.168.1.1 -nolookup
```

### Aggiornare il database di Nikto
```bash
nikto -update
```

---

## 11. Esempi completi per lab

### Scansione completa con output HTML
```bash
nikto -h http://192.168.1.1 \
  -p 80,443,8080 \
  -o scan-completo.html \
  -Format html
```

### Scansione autenticata con proxy Burp
```bash
nikto -h http://target.com \
  -id admin:password \
  -useproxy http://127.0.0.1:8080 \
  -o risultati.txt
```

### Scansione rapida focalizzata su vulnerabilità comuni
```bash
nikto -h http://192.168.1.1 \
  -Tuning 234 \
  -o vulnerabilita.csv \
  -Format csv
```

### Scansione con evasione IDS
```bash
nikto -h http://target.com \
  -evasion 1234 \
  -Tuning x6 \
  -o risultati.txt
```
