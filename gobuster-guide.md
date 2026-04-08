# Gobuster — Guida ai Principali Comandi e Test

> **Avviso legale:** Gobuster è uno strumento per la scoperta di directory, file e sottodomini. Utilizzarlo esclusivamente su target di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
gobuster --version
```

### Installazione manuale
```bash
apt update && apt install -y gobuster
```

### Da sorgente
```bash
go install github.com/OJ/gobuster/v3@latest
```

---

## 2. Modalità disponibili

| Modalità | Descrizione                                          |
|----------|------------------------------------------------------|
| `dir`    | Brute force di directory e file su web server        |
| `dns`    | Brute force di sottodomini DNS                       |
| `vhost`  | Brute force di virtual host                          |
| `fuzz`   | Fuzzing generico con placeholder `FUZZ`              |
| `s3`     | Enumerazione di bucket Amazon S3                     |
| `gcs`    | Enumerazione di bucket Google Cloud Storage          |

---

## 3. Modalità DIR — Directory e file

### Sintassi base
```bash
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
```

### Specificare le estensioni da cercare
```bash
gobuster dir -u http://target.com -w common.txt -x php,html,txt,bak
```

### Aumentare i thread (default 10)
```bash
gobuster dir -u http://target.com -w common.txt -t 50
```

### Aggiungere un cookie di sessione
```bash
gobuster dir -u http://target.com -w common.txt -c "PHPSESSID=abc123"
```

### Aggiungere un header personalizzato
```bash
gobuster dir -u http://target.com -w common.txt -H "Authorization: Bearer token123"
```

### Usare un User-Agent personalizzato
```bash
gobuster dir -u http://target.com -w common.txt -a "Mozilla/5.0"
```

### Usare un proxy (es. Burp Suite)
```bash
gobuster dir -u http://target.com -w common.txt --proxy http://127.0.0.1:8080
```

### Ignorare certificati SSL non validi
```bash
gobuster dir -u https://target.com -w common.txt -k
```

### Filtrare per codice di stato HTTP (mostrare solo specifici)
```bash
gobuster dir -u http://target.com -w common.txt -s "200,204,301,302,307,401,403"
```

### Escludere lunghezze di risposta specifiche (per ridurre falsi positivi)
```bash
gobuster dir -u http://target.com -w common.txt --exclude-length 1234
```

### Salvare i risultati su file
```bash
gobuster dir -u http://target.com -w common.txt -o risultati.txt
```

### Modalità verbosa (mostra tutti i tentativi)
```bash
gobuster dir -u http://target.com -w common.txt -v
```

---

## 4. Wordlist consigliate per la modalità DIR

| Wordlist                                              | Utilizzo                        |
|-------------------------------------------------------|---------------------------------|
| `/usr/share/wordlists/dirb/common.txt`               | Generale, veloce                |
| `/usr/share/wordlists/dirb/big.txt`                  | Più ampia                       |
| `/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt` | Media copertura     |
| `/usr/share/dirbuster/wordlists/directory-list-2.3-small.txt`  | Rapida              |
| `/usr/share/seclists/Discovery/Web-Content/raft-large-files.txt` | File comuni       |
| `/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt` | Directory |

---

## 5. Modalità DNS — Sottodomini

### Brute force sottodomini
```bash
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Mostrare anche gli IP risolti
```bash
gobuster dns -d target.com -w subdomains.txt -i
```

### Usare un resolver DNS specifico
```bash
gobuster dns -d target.com -w subdomains.txt -r 8.8.8.8
```

### Wordlist consigliate per DNS
```bash
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
/usr/share/seclists/Discovery/DNS/fierce-hostlist.txt
```

---

## 6. Modalità VHOST — Virtual Host

### Brute force di virtual host
```bash
gobuster vhost -u http://192.168.1.1 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Con dominio base specificato
```bash
gobuster vhost -u http://192.168.1.1 -w subdomains.txt --domain target.com
```

### Escludere lunghezze di risposta (per filtrare risultati uguali)
```bash
gobuster vhost -u http://192.168.1.1 -w subdomains.txt --exclude-length 612
```

---

## 7. Modalità FUZZ — Fuzzing generico

Il placeholder `FUZZ` viene sostituito con ogni valore della wordlist:

### Fuzzing parametri GET
```bash
gobuster fuzz -u "http://target.com/page.php?FUZZ=value" -w params.txt
```

### Fuzzing valori di parametri
```bash
gobuster fuzz -u "http://target.com/page.php?id=FUZZ" -w /usr/share/wordlists/dirb/common.txt
```

---

## 8. Esempi completi per lab

### Ricognizione web completa
```bash
gobuster dir \
  -u http://192.168.1.1 \
  -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt \
  -x php,html,txt,bak,zip \
  -t 40 \
  -o dir-scan.txt
```

### Scan di applicazione WordPress
```bash
gobuster dir \
  -u http://target.com \
  -w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt \
  -t 30 \
  -o wordpress-scan.txt
```

### Enumerazione sottodomini completa
```bash
gobuster dns \
  -d target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
  -t 50 \
  -i \
  -o dns-scan.txt
```

### Scan su HTTPS con proxy e cookie
```bash
gobuster dir \
  -u https://target.com \
  -w /usr/share/wordlists/dirb/big.txt \
  -k \
  -c "session=abc123" \
  --proxy http://127.0.0.1:8080 \
  -o https-scan.txt
```
